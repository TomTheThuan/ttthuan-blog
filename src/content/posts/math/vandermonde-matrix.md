---
title: Vandermonde Matrix Derivation
published: 2026-10-29
description: 'To solve the coefficient of each term in a higher order linear ODE'
image: ''
tags: []
category: ''
draft: false
lang: 'zh_CN'
---

Reference: [Solving General High-Order, Linear Ordinary Differential Equations (ODEs) - YouTube](https://www.youtube.com/watch?v=vA3MjD0cfS4)

A **Vandermonde matrix** is a structured matrix whose columns (or rows) are successive powers of a set of distinct numbers. It is the natural coefficient matrix for polynomial interpolation: solving a linear system with a Vandermonde matrix recovers the coefficients of a polynomial that passes through prescribed data points.

General expression of higher order linear ODE:

$$
a_n x^{(n)} + a_{n-1} x^{(n-1)} + \dots + a_2 \ddot{x} + a_1 \dot{x} + a_0 x = 0
$$

For $x = e^{\lambda t}$, $\lambda$ has $n$ possible solutions:

$$
\begin{aligned}
x(t) &= c_1 e^{\lambda_1 t} + c_2 e^{\lambda_2 t} + \dots + c_{n-1} e^{\lambda_{n-1} t} + c_n e^{\lambda_n t} \\
\dot{x}(t) &= \lambda_1 c_1 e^{\lambda_1 t} + \lambda_2 c_2 e^{\lambda_2 t} + \dots + \lambda_n c_n e^{\lambda_n t} \\
x^{(n)}(t) &= \lambda_1^n c_1 e^{\lambda_1 t} + \lambda_2^n c_2 e^{\lambda_2 t} + \dots + \lambda_n^n c_n e^{\lambda_n t}
\end{aligned}
$$

For initial condition: at $t = 0$.

$$
\begin{aligned}
x(0) &= c_1 + c_2 + \dots + c_n \\
\dot{x}(0) &= \lambda_1 c_1 + \lambda_2 c_2 + \dots + \lambda_n c_n \\
x^{(n)}(0) &= \lambda_1^n c_1 + \lambda_2^n c_2 + \dots + \lambda_n^n c_n \\
x^{(n-1)}(0) &= \lambda_1^{n-1} c_1 + \lambda_2^{n-1} c_2 + \dots + \lambda_n^{n-1} c_n
\end{aligned}
$$

> $n$ equations in total.
>

Transform into a matrix expression.

$$
\begin{bmatrix}
x(0) \\
\dot{x}(0) \\
\vdots \\
x^{(n-1)}(0)
\end{bmatrix}
=
\begin{bmatrix}
1 & 1 & \dots & 1 \\
\lambda_1 & \lambda_2 & \dots & \lambda_n \\
\vdots & \vdots & \ddots & \vdots \\
\lambda_1^{n-1} & \lambda_2^{n-1} & \dots & \lambda_n^{n-1}
\end{bmatrix}
\begin{bmatrix}
c_1 \\
c_2 \\
\vdots \\
c_n
\end{bmatrix}
$$

$$
\begin{aligned}
\implies D &= Mc \\
c &= M^{-1}D
\end{aligned}
$$

- $M$ is a $n \times n$ matrix, it's produced from the ODE solutions.
- $D$ is the data of initial condition.
- $c$ is the target prefix matrix.

Final Result: $c = M^{-1}D$
