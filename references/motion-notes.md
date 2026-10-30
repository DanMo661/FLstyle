# 动效完整实测参数 + 录屏抽帧观测法

数据来源：基准应用桌面版 30fps 录屏帧差分实测 + 源码精读互证。SKILL.md B7 只保留可直接执行的结论；本文件是完整参数与观测工具链。

## 移动端路由过渡（源码 page_transition.dart）

- 页面 push = **450ms 位移 25% 宽 + 淡化**
- slide 曲线 easeInOutCubicEmphasized
- fadeIn：Interval(0.15, 0.55)，easeOutCubic；fadeOut：Interval(0, 0.3)；两页交接点 0.2
- **fade 用表面色叠加实现而非 opacity**（省离屏渲染）。网页复刻用 background-color 过渡近似，同理更快

## 其余源码参数

- 弹簧物理（导航透镜/拖拽类）：stiffness 120–300，dampingRatio 0.35–0.7
- 果冻拉伸：拉伸 0.16 / 压扁 0.12
- AnimatedSwitcher：easeOut / easeIn 曲线对；图标切换 easeInBack + easeOutBack 回弹对
- 显隐：Easing.standardDecelerate
- 骨架屏 1.6s 呼吸；卡片 stagger 每张 +55ms（空态错峰实测 ~80ms/个，同量级）

## 录屏抽帧观测法（看不到动效时自己造眼睛）

1. **录屏**（Windows gdigrab）：

   ```
   ffmpeg -f gdigrab -framerate 30 -offset_x 60 -offset_y 60 -video_size 1300x1000 -i desktop -t 8 rec.mp4
   ```

   `pip install imageio-ffmpeg` 自带的 ffmpeg 即含 gdigrab，不必另装。
2. **抽帧**：`ffmpeg -i rec.mp4 -vf fps=15 frames/f%03d.png`
3. **拼胶片图**：PIL 把帧按序拼网格，每格左上角写帧号，交给视觉模型。
4. **判读要点**：
   - 让视觉模型做**相邻帧 diff** 自找过渡起止（非零像素区域 = 运动中），比人工猜帧窗省力
   - 驱动点击的自动化脚本常自带 ~2s 前置延时（窗口恢复/置前的 sleep），**取帧窗要 +2s 偏移**
   - 输出口径统一为四元组：方向 / 幅度 / 时长 / 曲线
5. **回源码补**：帧证据判不了曲线细节时，`grep "Curves\.\|Duration(milliseconds"` 统计 + 精读动效组件目录（fade_box / animated_visibility / animated_entries / navigator / page_transition）。
