# 比对验收与双平台实现 · 高发坑清单

两轮实战（网页 demo 五轮比对 + PySide6 桌面应用三轮比对 + nitpick 19 修）沉淀。比对前过一遍，能省掉大部分返修轮次。

## Web / 无头截图

- **innerHTML 整体替换会销毁内部监听器** → 用事件委托，或替换后重挂。
- **骨架屏整体替换 innerHTML 会吞掉脚本载入期的属性/元素注入**——注入必须挂在骨架还原回调之后，不能在脚本载入期直接插。
- `setPointerCapture(合成 pointerId)` 抛 InvalidPointerId → try-catch 包住。
- IAB/后台标签 rAF 全停 → 判"功能坏了"之前先查 `document.visibilityState`。
- localStorage 读入的数据做白名单校验，别直接进 DOM。
- 隐藏页用 `visibility:hidden` 时要同步移出 Tab 焦点序。
- 无头截图：JS 定时器驱动的界面加 `--virtual-time-budget=4000`（默认截图时机在 load，骨架屏还没被替换）；dev 服务器必须发 no-cache 头，否则 Chromium 启发式缓存吃掉静态资源改动。
- 官方 repo 里的 preview 图常是**带设备框的营销合成图**——只可裁出屏幕区域使用，不当裸样本；视觉模型给的裁剪坐标两次可能矛盾，重要样本裁完要验一次。

## PySide6 / Qt 桌面

- **QSS font-size 的 px 是物理像素**：150% DPI 下写 11px ≈ 7 逻辑像素的蚂蚁字，且不进 sizeHint。QSS 一律不写字号，用 `setFont` + `setPixelSize`（逻辑像素）。
- **QSS border-radius 在 Win11+150%DPI 实测失效** → 圆角和卡片底一律 QPainter 自绘；自绘控件不要调 `super().paintEvent`。
- **`QColor("rgba(r,g,b,a)")` 静默变黑**：QColor 构造器不认 rgba() 字符串，不报错、直接给黑色。写一个 rgba() 字符串解析器统一入口。
- **页面总高超出窗口时布局会压缩控件**（QLabel 被压到 9px 高、11px 字溢出成"叠印"假象）→ 页面套 QScrollArea + 尺寸瘦身；用 QGridLayout 探针读实际 geometry 验证，别信肉眼。
- **深色主题半生效**：所有自绘容器要收进 retheme 名单（含后加的），递归整棵子树；QPalette 必须整套随主题设置，漏了 appbar/菜单仍是系统色。
- **跨线程共享队列**：后台线程写 deque、UI 线程迭代时抛 RuntimeError → 锁 + `snapshot()`（拷贝后再迭代）。
- 渲染大日志：全量 setHtml 会冻结 UI → 批量拼接 + 只渲染尾部 N 条（如 500），滚屏加载更早的。
- 主题切换时分段器/开关等复合控件的状态要双向同步（改状态要刷 UI、点 UI 要改状态），否则换肤后显示错档。

## 流程类

- 打分验收（"像度 9/10"）无效——只有逐区块差异清单清零能验收。
- 视觉模型单轮描述不可信，**两个视觉代理对同一元素可能给出相反结论**（实测：分段器"分离药丸"vs"连续轨道"各执一词、OFF 开关 thumb 色差一档、正圆容器被判圆角方、选中卡的角上徽章被脑补成"蓝色描边"）。争议色值/形状必须**像素采样裁决**（PIL 取扫描线/角部探针，几分钟的事）。
- **DPI 不齐 = 成片假差异**：样本是 150% DPI 物理像素，无头截图默认 1x——侧栏宽/开关尺寸/卡高全部对不上。比对前先 `--force-device-scale-factor=1.5 --window-size=逻辑宽,逻辑高` 对齐。
- 像素采样要选准参照点：卡缝、圆角外、阴影带都可能撞色；先扫纵向/横向找卡片边界再取样。
- 桌面 exe 与参考软件同屏对比时，先 MoveWindow 定位再 PrintWindow，坐标 = 图内坐标 + 窗口原点。
- **禁止在用户正在使用的桌面上注入鼠标/键盘**（PostMessage 对 Flutter 窗口也无效；AppActivate+点击会跟用户真人输入打架）。要交互数据态：要么走无头路线，要么直接读源码常量——源码常量比截图测量更准。
- App 的自绘标题栏窗口（borderless）client 区≈全窗口，非客户区偏移≈0，坐标换算别按标准窗口算。
- **自动化实操经验（FlClash 实战验证）**：①侧栏有收起按钮，收起后所有坐标失效——点击前后先验证锚点颜色（如 x200 处应为侧栏色）；②键盘 SendKeys 走系统 IME，中文输入法状态下会把 `:` 打成全角 `：`、吞掉数字——绕 IME 用 SendInput `KEYEVENTF_UNICODE` 直注；③UIA 对 Flutter 窗口拿不到语义树（只有 FLUTTERVIEW pane），对现代文件对话框 FindAll 会挂死——现代文件框走键盘，Flutter 文本框走"点击聚焦+SendKeys"；④文件列表里点两下可能触发"重命名"对话框，会截胡后续键盘输入——弹窗先枚举 `#32770` 标题再操作；⑤PostMessage WM_COMMAND(IDCANCEL=2) 关 Win32 对话框可靠；⑥Win32 对话框 UIA 可用（ValuePattern/InvokePattern），现代 IFileDialog 不行。
