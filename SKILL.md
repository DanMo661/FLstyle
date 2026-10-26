---
name: FLstyle
description: 工具类 UI（网页或桌面 exe）的前端审美体系与落地规范。当用户要"精致的、iOS 风的、正经软件那样的"界面，或要求把某个工具上位机/调试器做成成熟软件时使用。含实测基准样本库、数字标尺版面骨架、可抄骨架代码、双平台映射与截图比对验收闭环。v1 内置工具类风格包（shots/），后续风格包按 `shots-<name>/` 扩展。
---

# FLstyle 前端审美体系与复刻流程

v1 工具类风格包：成熟开源工具软件源码精读 + **v0.9.2 Windows 真机截图/录屏逐像素实测**（2026-10-26）+ 两轮实做复盘提炼（基准参考应用见 README 致谢）。

## 0. 黄金样本库（先看图，再动手）

真机截图在本仓库 `shots/`（1400×1466 物理 px，系统 150% DPI，**逻辑 px = 物理 ÷ 1.5**，本文所有数字均为逻辑 px）：

| 文件 | 内容 | 用来对照什么 |
|---|---|---|
| `01-main.png` | 浅色·仪表盘 | 主参考：hero 状态卡 + 双列网格 + 分段器 + 开关行 |
| `02-profiles.png` / `03-connections.png` | 浅色·空状态页 | 插图+粗标题+胶囊按钮的空态范式 |
| `04-tools.png` | 浅色·工具页 | 分组列表卡范式（设置类页面长这样） |
| `05-theme.png` | 浅色·子页面 | 返回圆钮 + 预览大卡 + 三选一列表 |
| `06-dark-dashboard.png` | 深色·仪表盘 | 深色全套表面色阶 |
| `07-dark-theme.png` | 深色·列表页 | 深色列表卡/分隔线/分组标题 |
| `golden-mobile-dark.png` | 官方营销合成图裁出的手机屏 | 移动端深色参考（合成图原图带设备框，不当裸样本） |

**铁律：动手前让视觉模型（Vision-check 子代理，5.3-Flash）读对应黄金样本；验收时拿成品截图与样本逐区块比对。"像不像"以图为证，绝不信文字描述（包括本文件）。** 本 skill 曾因纯靠源码文字转述、生成全程无视觉，连续两次被用户判"完全不像"。

## 1. 设计宪法（实测修订版）

> **结构靠灰阶表面分层，颜色只给状态**（选中/成功/警告/错误/一屏唯一主动作）。

推论：卡片用"与背景拉开一档的填充"区分层级，不画实线边框；分隔线 = 前景色低透明而非实色；
彩色永不爬上背景；数值状态色只上文字/小图标/小 chip，不满染整卡。

**嵌套方向规则（10-26 实测修正，原"暗色只加亮不回退"是错的）**：
嵌套分两种，方向不同——
- **轨/凸块**（分段器轨道、segment 轨、输入轨、**网速双子卡**）：永远"往中灰方向"走。浅色 = 白卡上变暗一档（L≈91）；深色 = 灰卡上变亮一档（L18~21 vs 卡 L14~16）。两模式下绝对值都落在中性灰带。
- **井/凹槽**（**「局域代理」类控制子条**、图标井）：**永远比宿主深一档**，浅深色一致（浅色 白卡→L88 灰条；深色 L14 卡→L11 子条）。"读成洞"是故意的，别修掉。
- 两向嵌套按**语义**选：承载内容的子块=轨（抬起来），承载开关/附属控制的子条=井（凹下去）。
- 徽章/药丸底（CN 徽章、未运行、选中导航）：再高一档（深色 L23），是最亮的表面。

## 2. Tokens 实测表

**浅色**（窗口实测 1400×1466@150%）：
- 窗口/侧栏 `#F1F1F2`（L95，饱和度≈4%）；卡片 `#FFFFFF`；轨 L≈91 灰；井 L88；徽章底 L≈87
- 文字：标题/大数字 `#1A1C1E`（L11）；次要 `#757575`（L47）
- 强调蓝 `#1B6FD8` = HSL(213,75%,48%)（种子色引擎产物，换种子色全联动）；亮蓝（对勾/图标点缀）`#2196F3`
- 成功绿（药丸）底 `hsl(141,44%,20%)` + 字 `#4ADE80`；卡片圆角 16；卡内边距 24

**深色**（同窗口切换主题实测）：
- 标题栏 L6；侧栏 L8；内容区 L9；**卡 L14**；轨 L18；井 L11；徽章/选中药丸底 L23
- 文字 L95 / 正文 L88 / 次要 L62；分隔线/描边 L20（1px）
- 强调蓝与浅色同值（L48）；选中导航药丸 = 中性灰 L23（不上色）；选中项左缘 3px 蓝竖条
- 开关：ON 轨=强调蓝白滑块；OFF 轨=L25 灰、**滑块 L62 浅灰（深色下 OFF 滑块是亮的）**
- 纯黑模式：表面 tone 全压 0，层级差保留（源码结论，未实测）

其余沿用：语义色固定不随种子调和（success `hsl(122,45%,34%/70%)`、warning `hsl(35,90%,38%/68%)`、error `hsl(4,90%,48%/68%)`）；等宽数字 tabular-nums 用于一切跳动数值，大数字字距 -0.02em；按钮一律胶囊，chip 圆角 8。

## 3. 版面骨架（数字标尺，全部逻辑 px）

**窗口骨架**：
- 自绘标题栏 31 高，右侧窗控（图钉/最小化/最大化/关闭）
- 侧栏 220 宽：图标列 x≈24、文字 x≈49；导航行高 36、行距 8
- 选中态解剖：通栏灰药丸（浅 L87/深 L23，圆角全圆）+ **窗口最左缘 3px 蓝竖条（高≈36）** + 图标文字加深加粗
- 内容区水平边距 ~20；页头行高 ~75：页标题 24px 粗体左对齐 + 右上动作区（⌀37 圆钮或胶囊工具条，工具条内图标间距 43）

**仪表盘网格**（内容区宽 ~657，参考 01-main）：
1. hero 通栏状态卡 高 ~204：左信息列（16px 徽章药丸排 → 31px 大数字 → 14px 次要行）+ 右侧 ⌀101 大圆操作钮，卡内边距 24
2. 双列卡网格：卡宽 320、列隙 18、行距 19；卡内解剖 = 标题行（16px 标题 + 右侧 14px"峰值 xx"小字）→ 42px 等宽大数字 → 图表区吃满余高
3. 通栏分段器卡：高 ~58，三段胶囊全圆，选中段实心蓝白字，未选中段坐在轨色上
4. 开关行卡：高 ~61 = 40 方圆角图标井 + 主文字 16 + 副文字 14 + 右开关（轨 52×32）
5. 双列继续往下铺：可点整卡（"添加配置"= 大字 + 右箭头）、列表小卡

**分组列表页**（设置/工具类，参考 04-tools）：
- 单列通栏卡；行高 56（图标 x43、文字 x69、右侧 chevron x656）；行间 1px 分隔线（前景 20% 透明）
- 分组标题 13px 灰，**在卡外上方**，距上一卡 24
- 卡下方可跟 1-2 行 12px 注释灰字

**空状态**（参考 02/03）：居中 ⌀~200 圆形浅蓝插图 + 16px 粗标题 + 14px 副文 + 白胶囊按钮（高 ~40、带 ⊕ 图标）。**空态也是完整设计，不是"暂无数据"四个字。**

**字阶**：24 页标题 / 16 正文 / 14 次要 / 13 分组标题 / 42 大数字 / 31 次大数字。间距一律 4 的倍数，主节奏 8/16/24。

**编排原则**（结构层的老经验，仍有效）：
- 非对称主网格：hero 通栏在上、双列在中、通栏控件在下——**不是**等高卡片竖排
- 渐进披露：低频表单进子页/滑出面板，主页只留高频读数和主动作
- 一个页面一个视觉焦点（最大字号/最大图形只出现一次）
- 语义别照抄构图：基准应用流量卡"↑灰↓彩"是方向语义；两个平权通道应同字重+各自系列色点

**形状语言与图标（二轮比对抓出的"最后一英里"，漏了立刻不像）**：
- 启停大钮/国别徽章 = **花形 scalloped seal（瓣外鼓，观感 10~12 瓣）**，不是正圆；小徽章（<30px）瓣数降到 5~6 或退回圆形，9 瓣在小尺寸读作圆
- 卡头与分段器每段都带 14px 小图标（状态=折线 / 网速=表盘 / 出站模式=分支 / 规则·全局·直连各配 glyph）——纯文字卡头是最常见的"差一口气"
- 功能图标井 = **中性井底 + 强调色描边图形**（暗底亮图形），不是实心彩底白图形
- 状态徽章紧跟标题文字同一行，不贴卡片右缘；IP 下的网络类型/质量是纯文本，不是药丸串
- 花形路径可用 SVG 弧段程序生成（n 点圆周 + A 弧外鼓，弦长 2R·sin(π/n) ≤ 2r 即有效）

## 3.5 窗口拉伸与重排（真机五档实测 + 源码互证）

断点（逻辑 px，源码 constant.dart）：**mobile ≤600 / laptop ≤840 / desktop >840**（ViewMode 三档）。

| 逻辑宽 | 导航 | 内容区 | 侧栏宽 | 卡距 |
|---|---|---|---|---|
| ≤600 | **悬浮底部胶囊 tab，只留 3 项**（次要页面无入口；胶囊叠在滚动内容之上，距底 ~23） | 1 列；网速=父卡内两块并排子卡 | 无 | 8 |
| ≤840 | 图标-only rail | 1 列（同上） | 48 | 12 |
| >840 | 图标+文字侧栏，**定宽 220 不随窗口增长** | 多列网格；网速父卡解体成两张独立卡（「峰值」标签此时才出现） | 220 | 16 |

重排铁律（复刻工具 UI 时照抄）：
- **列数=内容驱动公式**：代理页 `columns = max(ceil(viewWidth/250), 2)`（±1 由密度设置调）；仪表盘 >840 后 2 列起步，更宽升 4 列单元网格（特性卡跨 2）
- **字号永不缩放**——空间不够就截断文本/丢次要 chip，绝不缩小控件；hero 大圆钮直径恒定钉在卡右，牺牲的是旁边的文字
- 侧栏标签只在 desktop 档出现（源码注释：laptop 展开侧栏会让内容比 mobile 档还窄）
- 桌面专属控件随档位显隐（如铅笔编辑钮仅窄档存在）
- 复刻：CSS 用 container query / JS 宽度观察切三档；PySide6 在 resizeEvent 里按 600/840 换布局；黄金样本 `shots\resize-*.png`（560/700/1000/1400/1800 物理）

## 4. 骨架代码（抄这个，改内容）

仪表盘骨架（HTML+CSS，token 变量按第 2 节填值）：

```html
<style>
:root { --page:#F1F1F2; --card:#fff; --track:#E8E8E9; --well:#E1E1E2; --badge:#DEDEDF;
        --fg:#1A1C1E; --fg2:#757575; --accent:#1B6FD8; --line:rgba(26,28,30,.14);
        --r-card:16px; --gap:18px; }
[data-theme=dark]{ --page:#161616; --card:#242424; --track:#2F2F2F; --well:#1B1B1B;
        --badge:#3A3A3A; --fg:#F2F2F2; --fg2:#9E9E9E; --line:rgba(242,242,242,.2); }
body{margin:0;font:14px/1.5 system-ui;background:var(--page);color:var(--fg)}
.app{display:flex;height:100vh}
.rail{width:220px;padding:16px 0;display:flex;flex-direction:column;gap:8px}
.rail a{display:flex;align-items:center;gap:13px;height:36px;padding:0 26px;
        border-radius:18px;color:var(--fg2);text-decoration:none}
.rail a.on{background:var(--badge);color:var(--fg);font-weight:600;box-shadow:inset 3px 0 0 var(--accent)}
/* inset 竖条在窗口左缘：给 .rail 加 padding-left:3px、药丸 margin-left:-16px 压到缘 */
.main{flex:1;padding:0 20px;overflow:auto}
.pagehead{height:75px;display:flex;align-items:center;justify-content:space-between}
.pagehead h1{font-size:24px;margin:0}
.grid{display:grid;grid-template-columns:1fr 1fr;gap:19px 18px}
.card{background:var(--card);border-radius:var(--r-card);padding:24px}
.card.span{grid-column:1/-1}
.hero{min-height:204px;display:flex;justify-content:space-between}
.hero .num{font-size:31px;font-variant-numeric:tabular-nums;letter-spacing:-.02em}
.kpi .num{font-size:42px;font-variant-numeric:tabular-nums}
.kpi .row1{display:flex;justify-content:space-between;color:var(--fg2);font-size:14px}
.seg{display:flex;background:var(--track);border-radius:29px;padding:5px;height:58px}
.seg button.on{background:var(--accent);color:#fff;border-radius:24px}
.switchrow{display:flex;align-items:center;gap:16px}
.switchrow .ic{width:40px;height:40px;border-radius:10px;background:var(--accent);color:#fff;
        display:grid;place-items:center}
.listcard .row{display:flex;align-items:center;gap:26px;height:56px}
.listcard .row+.row{border-top:1px solid var(--line)}
.group-title{font-size:13px;color:var(--fg2);margin:24px 0 8px}
</style>
<div class="app">
  <nav class="rail"><a class="on">仪表盘</a><a>配置</a><a>实时连接</a><a>工具</a></nav>
  <main class="main">
    <div class="pagehead"><h1>仪表盘</h1><button class="round-btn">✓</button></div>
    <div class="grid">
      <section class="card span hero">…左信息列+右大圆钮…</section>
      <section class="card kpi">…标题行+42px大数字+图表…</section>
      <section class="card kpi">…</section>
      <section class="card span seg">…三段胶囊…</section>
      <section class="card switchrow">…图标井+文字+开关…</section>
    </div>
  </main>
</div>
```

（抄时逐块对照 01-main.png 落内容，结构为准。）

## 5. 动效（源码 + 录屏抽帧实测互证）

**性格一句话：Material 的皮、工具软件的心——只在"值"（颜色/透明度）上做渐变，从不在"位置"上做动画。**

桌面版 30fps 录屏帧差分实测：
- **根级导航切换 = 0ms 硬切**（一帧整页替换、选中 pill 同帧跳位，无滑入无退场）——复刻时**别**给根导航加转场
- **push 下钻子页 ≈133ms 原位交叉淡化**（新页原地叠上，零位移）
- **分段器选中迁移 = 原位淡入淡出 150~250ms**（旧 pill 1 帧归零、新 pill 1~2 帧淡入带 +18% 按压残影；**没有滑动滑块**，别做滑动的 thumb）
- **主题切换 ≈250ms 全窗均匀色彩 lerp**（九宫格逐帧同步爬升，非擦除非扩散；中段文字与底色互穿出一帧"泛白"低对比帧=整主题插值的签名；曲线≈Curves.ease）
- **hover ≤133ms 即现即灭**（比选中 pill 更淡的独立底色，可与选中叠加）；设置类列表行无 hover
- **唯一的回弹预算给内容入场**：空态图标 easeOutBack 弹簧缩放（+10% 过冲，200~270ms）+ 子元素错峰跟进 600ms（标题→副文→按钮→装饰点逐个 pop）

移动端路由（源码 page_transition.dart）：页面 push = 450ms 位移 25% 宽 + 淡化；slide 用 easeInOutCubicEmphasized，fadeIn Interval(0.15,0.55) easeOutCubic、fadeOut Interval(0,0.3)，两页交接点 0.2；**fade 用表面色叠加实现而非 opacity**（省 Impeller 离屏渲染；网页复刻用 background-color 过渡近似同理更快）
其余源码参数：弹簧物理（导航透镜/拖拽类）stiffness 120–300 / dampingRatio 0.35–0.7，果冻拉伸 0.16 / 压扁 0.12；AnimatedSwitcher easeOut/easeIn 对；图标切换 easeInBack+easeOutBack 回弹对；显隐 Easing.standardDecelerate；骨架屏 1.6s 呼吸；卡片 stagger 每张 +55ms（实测空态错峰 ~80ms/个，同量级）；阴影 blur 24 / y 8 / alpha 0.08（暗色 ×3）；滚动列表内卡片无阴影

**动效观测方法（看不到动效时自己造眼睛）**：
1. 录屏：`ffmpeg -f gdigrab -framerate 30 -offset_x 60 -offset_y 60 -video_size 1300x1000 -i desktop -t 8 rec.mp4`（Windows 下 imageio_ffmpeg 自带的 ffmpeg 即含 gdigrab，`pip install imageio-ffmpeg` 可得）
2. 抽帧 `-vf fps=15` → PIL 拼带帧号标签的胶片格 → Vision-check 读格判「方向/幅度/时长/曲线」四元组
3. 两个坑：驱动点击的 PS 脚本自带 ~2s 前置延时（窗口恢复/置前的 sleep），**取帧窗要 +2s 偏移**；与其自己猜帧窗，不如把全帧丢给视觉 agent 让它**相邻帧 diff** 找过渡起止（非零区域=运动中，实测最省力）
4. 帧证据判不了曲线细节时回源码补：`grep "Curves\.\|Duration(milliseconds"` 统计 + 精读 `ui/` 动效组件（fade_box/animated_visibility/animated_entries/navigator/page_transition）

## 6. 双平台落地映射

- **Web**：CSS 变量做 tokens；圆角近似超椭圆；`color-mix()` 做透明分隔线；spring 用 rAF + 半隐式欧拉（dt 钳 1/30），支持飞行中重定目标
- **PySide6 桌面**：QSS 只管颜色/字号/字族，**圆角和卡片底一律 QPainter 自绘**（Win11+150%DPI 下 QSS border-radius 实测失效）；自绘控件不调 super().paintEvent；主题切换 = 重建 token 字典 + 全部自绘控件逐个 retheme（漏一个深浅模式就穿帮）
- 布局尺寸：工具型窗口最小 1280~1320×820~900；所有面板一屏装下，靠网格+可折叠 dock+滑出面板

## 7. 截图比对验收闭环（核心工序，不可跳）

1. **成品截图**：
   - 网页：`chrome --headless=new --screenshot=out.png --window-size=1400,1466 <url>`（本地文件用 file://；改动频繁时 dev 服务器必须发 no-cache 头）
   - exe / 参考软件：PrintWindow 抓全窗口位图（`PrintWindow(hwnd, hdc, PW_RENDERFULLCONTENT)`，不怕遮挡/最小化；MoveWindow 先定位再抓）；驱动真实 UI 用 SetCursorPos + mouse_event 脚本点击（坐标 = 图内坐标 + 窗口原点）
2. **双图比对**（Vision-check 子代理，一次一焦点）：给「成品图 + 最接近的黄金样本」，提纲：
   a. 布局完整性（有无裁切/溢出/空白岛）→ b. **逐区块结构对照**（侧栏宽/卡宽/行高/边距/圆角，±10% 容差）→ c. 灰阶层级（四级表面是否都认得出）→ d. 密度与字阶（大小数字层次、留白节奏）→ e. 状态样式（选中/开关/分段器）
   要求输出**按影响排序的差异清单**，每条带双方图上的证据坐标
3. **返修 → 复截 → 复比**，直到差异清单里没有结构性条目（色差 |ΔL|≤3 视为过）
4. **打分无效**："9/10"证明不了像不像，只有"差异清单清空"能。旧流程的纯打分验收就是连续翻车的直接原因
5. nitpick/新用户实测挑刺两轮照跑，已知高发坑：innerHTML 整体替换销毁监听器（事件委托）、**骨架屏整体替换 innerHTML 会吞掉脚本载入期的属性注入——注入必须挂在还原回调之后**、setPointerCapture(合成 pointerId) 抛 InvalidPointerId（try-catch）、IAB 后台 rAF 全停先查 visibilityState、localStorage 读入白名单校验、隐藏页 visibility:hidden 移出 Tab 序
6. 无头截图细节：JS 定时器驱动的界面加 `--virtual-time-budget=4000`（默认截图时机在 load，骨架屏还没被替换）；官方 snapshots 是**带设备框的营销合成图**，只可裁屏使用；视觉模型给的裁剪坐标两次可能矛盾，重要样本裁完要验一次

## 8. 全流程

1. **看图**：Vision-check 读黄金样本库中与任务最接近的 1-2 张，先出结构笔记
2. **tokens 先行**：按第 2 节填变量，再写组件
3. **抄骨架**：以第 4 节骨架为底，逐块替换内容；不自由发挥结构
4. **比对闭环**（第 7 节），修到清单清空
5. **nitpick 两轮** + git commit 先于"完成"宣告，实测证据（截图）随交付陈述
