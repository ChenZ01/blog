---
title: UE5 研究
date: 2025-10-21 02:17:15
tags:
categories: Learning
math: true
---
记录对 UE5 的研究
<!-- more -->

使用 `HighResShot 4` 截图时，实际就是将全局的 `GScreenshotResolutionX/Y` 乘上 4，也即 创建大小为 4x 的 dummy viewport 完整渲染一帧，似乎不涉及 `SceneVisibility.cpp`

`ScreenPercentage` 并不影响最终截图的分辨率。以 1080p 为例
- `ScreenPercentage` 为 50 时，以 960×540 渲染，再通过 upscaling 算法放大到 1920×1080 输出
- `ScreenPercentage` 为 200 时，以 3840×2160 渲染，再通过 superscaling 算法缩回到 1920×1080 输出

Take Recorder 会将较大的绝对时间转成 float，再相减计算帧间隔。时间精度损失会改变采样结果。例如 60fps 的序列，起始帧 3118320 对应绝对时间 51972 秒。动画录制器代码中包含以下处理

```cpp
float CurrentSeconds = CurrentTime.AsSeconds();
// ...
float DeltaTime = CurrentSeconds - PreviousSeconds;
AnimationRecorder->Update(DeltaTime);
PreviousSeconds = CurrentSeconds;
```

在 51972 秒附近，相邻 float32 数值相差 **3.90625 ms**。原本均匀的 60fps 间隔 **16.6667 ms**，因此变成：

```text
15.625, 19.53125, 15.625, 15.625, 15.625, 19.53125, ... ms
```

至于为何不是交错，这是由于它将每个绝对时间分别四舍五入到浮点刻度，再计算相邻差值，并不是轮流选择两个间隔。浮点刻度 3.90625 的理想帧间隔对应 $$\dfrac{16.6667}{3.90625} = 4.2667$$ 个刻度。所以通常前进 4 个刻度（15.625 ms），偶尔需要前进 5 个刻度（19.53125 ms），才能跟上理想时间

动画录制器用这些间隔重新采样骨骼和组件变换；在根运动转写分支中，结果又进入人物 Transform 轨道。时间误差就变成了位置波动。

关闭 `Start at Current Timecode` 即可。