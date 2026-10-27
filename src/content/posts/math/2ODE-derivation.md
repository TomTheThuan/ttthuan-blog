---
title: Derive the Solution of Second Order Differential Equation
published: 2026-10-27
description: '推导Homologous Second Order Differential Equation的解'
image: ''
tags: ["数学"]
category: '#数学'
draft: false
lang: 'zh_CN'
---

Homologous Second Order Differential Equation的基本形式：

$$
\ddot{x} + a\dot{x} + bx = 0
$$

> 主要的思路：配出一个$\dot{x} - x = 0$，将second order变为first order equation来做。

$$
(\dot{x} - \alpha x)' - \beta(\dot{x} - \alpha x) = 0
$$
拆括号并合并同类项：
$$
\begin{aligned}
&\ddot{x} - (\alpha + \beta)\dot{x} + \alpha \beta x = 0 \\
&\begin{cases}
\alpha + \beta = -a \\
\alpha \beta = b
\end{cases}
\end{aligned}
$$

Let $\lambda$ be the roots, $\alpha, \beta = \lambda$：

$$
\lambda^2 + a\lambda + b = 0
$$

$$
\begin{cases}
\alpha = \frac{-a - \sqrt{a^2 - 4b}}{2} \\
\beta = \frac{-a + \sqrt{a^2 - 4b}}{2}
\end{cases}
$$

Let $y = \dot{x} - \alpha x$：

$$
\begin{aligned}
\dot{y} - \beta y &= 0 \\
y e^{-\beta t} &= y_0 \\
y &= y_0 e^{\beta t} \\
\dot{x} - \alpha x &= (\dot{x}_0 - \alpha x_0) e^{\beta t} \\
(x e^{-\alpha t})' &= (\dot{x}_0 - \alpha x_0) e^{\beta t} e^{-\alpha t}
\end{aligned}
$$

得出：

$$
x e^{-\alpha t} = x_0 + \int_0^t (\dot{x}_0 - \alpha x_0) e^{(\beta - \alpha)t} dt
$$

现在需要分别讨论$\alpha$和$\beta$的情况：
$$
\begin{aligned}
\alpha &\neq \beta, & \alpha, \beta &\in \mathbb{R} \\
\alpha &= \beta, & \alpha, \beta &\in \mathbb{R} \\
\alpha &\neq \beta, & \alpha, \beta &\in \mathbb{Z}
\end{aligned}
$$
## 1. $\alpha \neq \beta,  \alpha, \beta \in \mathbb{R}$

$$
\begin{aligned}
x &= x_0 e^{\alpha t} + e^{\alpha t} \left[ \frac{\dot{x}_0 - \alpha x_0}{\beta - \alpha} e^{(\beta - \alpha)t} \right]_0^t \quad \text{（Assume } \alpha \neq \beta\text{）} \\
x &= x_0 e^{\alpha t} + \frac{\dot{x}_0 - \alpha x_0}{\beta - \alpha} e^{\alpha t} (e^{(\beta - \alpha)t} - 1) \\
x &= x_0 e^{\alpha t} + \frac{\dot{x}_0 - \alpha x_0}{\beta - \alpha} e^{\beta t} - \frac{\dot{x}_0 - \alpha x_0}{\beta - \alpha} e^{\alpha t} \\
x &= \left( x_0 - \frac{\dot{x}_0 - \alpha x_0}{\beta - \alpha} \right) e^{\alpha t} + \frac{\dot{x}_0 - \alpha x_0}{\beta - \alpha} e^{\beta t} \\
x &= \frac{\beta x_0 - \dot{x}_0}{\beta - \alpha} e^{\alpha t} + \frac{\dot{x}_0 - \alpha x_0}{\beta - \alpha} e^{\beta t} \\
&\sim Ae^{\alpha t} + B e^{\beta t}
\end{aligned}
$$


## 2. $\alpha = \beta, \alpha, \beta \in \mathbb{R}$

Let
$$
\begin{cases}
\alpha = m - ni \\
\beta = m + ni
\end{cases}
$$
where
$$
\begin{cases}
m = -\frac{a}{2} \\
n = \frac{1}{2}\sqrt{4b - a^2}
\end{cases}
$$


$$
\begin{aligned}
x &= \frac{1}{2ni} \left( (m x_0 + ni x_0 - \dot{x}_0) e^{(m-ni)t} + (\dot{x}_0 - m x_0 + ni x_0) e^{(m+ni)t} \right) \\
&= \frac{e^{mt}}{2ni} \left( (m x_0 + ni x_0 - \dot{x}_0) e^{-nit} + (\dot{x}_0 - m x_0 + ni x_0) e^{nit} \right)
\end{aligned}
$$

展开$e^{nit}$

$$
\begin{aligned}
x &= \frac{e^{mt}}{2ni} \left[ (m x_0 + ni x_0 - \dot{x}_0)(\cos nt - i \sin nt) + (\dot{x}_0 - m x_0 + ni x_0)(\cos nt + i \sin nt) \right] \\
&= \frac{e^{mt}}{2ni} (2ni x_0 \cos nt - 2m x_0 i \sin nt + 2\dot{x}_0 i \sin nt) \\
&= e^{mt} \left( x_0 \cos nt + \frac{\dot{x}_0 - m x_0}{n} \sin nt \right) \\
&\sim e^{mt} \left( A \cos nt + B\sin nt \right)
\end{aligned}
$$


## 3. $\alpha \neq \beta, \alpha, \beta \in \mathbb{Z}$

$$
\begin{aligned}
x e^{-\alpha t} &= x_0 + \int_0^t (\dot{x}_0 - \alpha x_0) dt \\
x e^{-\alpha t} &= x_0 + (\dot{x}_0 - \alpha x_0)t \\
x &= (x_0 + (\dot{x}_0 - \alpha x_0)t) e^{\alpha t} \\
x &= (x_0 + (\dot{x}_0 + \frac{a}{2} x_0)t) e^{-\frac{a}{2} t} \\
&\sim (A + Bt) e^{-\frac{a}{2} t}
\end{aligned}
$$