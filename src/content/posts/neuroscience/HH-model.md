---
title: From Nernst Equation to Hodgkin-Huxley Model, with Python complementation
published: 2026-10-04
description: ''
image: ''
tags: ["CNS", "技术", "计算机", "分享"]
category: '分享'
draft: true
lang: 'zh_CN'
---

## The Boltzmann Distribution

当温度 $T$ 处于equilibrium时，一个地方particle的密度与potential energy $U$ 的关系如下：
$$
n \propto e^{-U/kT}
$$

> $k$ 是Boltzmann constant

一个地方的能量越高，温度越高，粒子密度呈指数级下降（当 $U$ 为负数时）：

![image-20261004202725075](./HH-model.assets/image-20261004202725075.png)

