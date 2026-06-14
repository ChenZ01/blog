---
title: LaTex tricks
date: 2025-01-15 05:42:39
tags: 
categories: Learning
math: true
---
记录一些使用 LaTeX 的技巧
<!-- more -->
## 自适应下划线

```tex
\newcommand\dlmu[2][4cm]{\hskip1pt\underline{\hbox to #1{\hss#2\hss}}\hskip3pt}
```

然后 `\dlmu{Hello}` 如此调用即可

## Beamer 数学公式

Beamer 数学公式使用 Computer Modern Font 的方法，[ref](https://latex-beamer.com/tutorials/beamer-font/)

```\usefonttheme{professionalfonts}```

注意要用 xeLaTex，pdfLaTex 有点问题