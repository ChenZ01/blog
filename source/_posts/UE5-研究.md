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