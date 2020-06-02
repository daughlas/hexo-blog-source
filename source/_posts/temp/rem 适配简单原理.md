---
title: rem 适配简单原理
date: 
categories:
- 临时
tags:
- 临时
---

假如 设计图尺寸 375px

设置 1rem = 10px

有一个 50px 的元素

适配一个 360px 的屏幕，适配一个 480px 的屏幕



375px / 360px = 5rem * 10px/1rem / 5rem * Xpx/1rem

375px / 480px = 5rem * 10px/1rem / 5rem * Ypx/1rem



得出公示



设计图宽度 / 手机屏幕宽度 =  measuredSize / htmlFontSize



htmlFontSize = measuredSize * 手机屏幕宽度 / 设计图宽度