---
title: Reducing Higher Order Linear ODE Systems
published: 2026-10-29
description: 'Transform higher order linear ODE to system of n coupled 1st order ODEs.'
image: ''
tags: []
category: ''
draft: false
lang: 'zh_CN'
---

Reference: [Matrix Systems of Differential Equations - YouTube](https://www.youtube.com/watch?v=Vtijyyo5fKI)

> Central idea: express the higher order DE into a first order DE with vectors.

General form:
$$
a_n x^{(n)} + a_{n-1} x^{(n-1)} + \dots + a_2 \ddot{x} + a_1 \dot{x} + a_0 x = 0
$$

Introduce new variables:

$$
\begin{cases}
x_1 = x \\
x_2 = \dot{x} \\
x_3 = \ddot{x} \\
\vdots \\
x_n = x^{(n-1)}
\end{cases}
\quad \xrightarrow{\text{take derivative}} \quad
\begin{aligned}
\dot{x}_1 &= \dot{x} = x_2 \\
\dot{x}_2 &= \ddot{x} = x_3 \\
\dot{x}_3 &= \dddot{x} = x_4 \\
\vdots \\
\dot{x}_n &= x^{(n)}
\end{aligned}
$$

$$
\begin{aligned}
x^{(n)} &= -a_0 x - a_1 \dot{x} - a_2 \ddot{x} - \dots - a_{n-1} x^{(n-1)} \\
&= -a_0 x_1 - a_1 x_2 - a_2 x_3 - \dots - a_{n-1} x_n
\end{aligned}
$$

$$
\frac{d}{dt}
\begin{bmatrix}
x_1 \\
x_2 \\
x_3 \\
\vdots \\
x_{n-1} \\
x_n
\end{bmatrix}
=
\begin{bmatrix}
0 & 1 & 0 & \dots & 0 & 0 \\
0 & 0 & 1 & \dots & 0 & 0 \\
0 & 0 & 0 & \dots & 0 & 0 \\
\vdots & \vdots & \vdots & \ddots & \vdots & \vdots \\
0 & 0 & 0 & \dots & 0 & 1 \\
-a_0 & -a_1 & -a_2 & \dots & -a_{n-2} & -a_{n-1}
\end{bmatrix}
\begin{bmatrix}
x_1 \\
x_2 \\
x_3 \\
\vdots \\
x_{n-1} \\
x_n
\end{bmatrix}
$$

Let $\mathbf{x} = \begin{bmatrix} x_1 \\ x_2 \\ \vdots \\ x_n \end{bmatrix}$ and $A =
\begin{bmatrix}
0 & 1 & 0 & \dots & 0 & 0 \\
0 & 0 & 1 & \dots & 0 & 0 \\
0 & 0 & 0 & \dots & 0 & 0 \\
\vdots & \vdots & \vdots & \ddots & \vdots & \vdots \\
0 & 0 & 0 & \dots & 0 & 1 \\
-a_0 & -a_1 & -a_2 & \dots & -a_{n-2} & -a_{n-1}
\end{bmatrix}$, then $\dot{\mathbf{x}} = A\mathbf{x}$, the eigenvalues of $A$ are roots of characteristic polynomial.
