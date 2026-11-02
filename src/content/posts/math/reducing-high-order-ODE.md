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

## Convert General Linear ODE into Matrix Form

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

## Solving $\dot{\underline{x}} = \underline{\underline{A}} \space \underline{x}$ with Eigenvalues and Eigenvectors

### Example

> $x_r$ stands for a population, and it grows follows the trend of $\dot{x}_r = \lambda_r x_r$.

Write these equations in a system:

$$
\begin{cases}
\dot{x}_1 = \lambda_1 x_1 \\
\dot{x}_2 = \lambda_2 x_2 \\
\vdots \\
\dot{x}_n = \lambda_n x_n
\end{cases}
\implies
\frac{d}{dt}
\begin{bmatrix}
x_1 \\
x_2 \\
\vdots \\
x_n
\end{bmatrix}
=
\underbrace{
\begin{bmatrix}
\lambda_1 & 0 & \dots & 0 \\
0 & \lambda_2 & \dots & 0 \\
\vdots & \vdots & \ddots & \vdots \\
0 & 0 & \dots & \lambda_n
\end{bmatrix}
}_{\underline{\underline{D}}}
\begin{bmatrix}
x_1 \\
x_2 \\
\vdots \\
x_n
\end{bmatrix}
$$

Integrate (Solve $x$):

$$
\begin{cases}
x_1(t) = x_1(0) e^{\lambda_1 t} \\
x_2(t) = x_2(0) e^{\lambda_2 t} \\
\vdots \\
x_n(t) = x_n(0) e^{\lambda_n t}
\end{cases}
\implies
\begin{bmatrix}
x_1 \\
x_2 \\
\vdots \\
x_n
\end{bmatrix}(t)
=
\underbrace{
\begin{bmatrix}
e^{\lambda_1 t} & 0 & \dots & 0 \\
0 & e^{\lambda_2 t} & \dots & 0 \\
\vdots & \vdots & \ddots & \vdots \\
0 & 0 & \dots & e^{\lambda_n t}
\end{bmatrix}
}_{e^{\underline{\underline{D}} t}}
\begin{bmatrix}
x_1 \\
x_2 \\
\vdots \\
x_n
\end{bmatrix}(0)
$$

> This is a very special (ideal) case, which the coefficient matrix ${e^{\underline{D} t}}$ is a diagonal matrix.

### For general system $\dot{\underline{x}} = \underline{\underline{A}} \space \underline{x}$

> Transform the coordinate: $\underline{x} = \underline{\underline{T}} \space \underline{z}$
>
> Express $\underline{x}$ in terms of $\underline{z}$

1. $\dot{\underline{x}} = \underline{\underline{A}} \space \underline{x}$
   $\dot{\underline{z}} = \underline{\underline{D}} \space \underline{z}$

   > $\underline{\underline{D}}$ is a $n \times n$ diagonal matrix.

   $\underline{z}(t) = e^{\underline{\underline{D}} t} \underline{z}(0)$

2. $$
   \begin{aligned}
   \underline{x} &= \underline{T} \space \underline{z} \\
   \dot{\underline{x}} &= \underline{\underline{T}} \dot{\underline{z}} \\
   \underline{\underline{T}} \dot{\underline{z}} &= \underline{\underline{A}} \underline{\underline{T}} \underline{z} \\
   \dot{\underline{z}} &= \underline{\underline{T}}^{-1} \underline{\underline{A}} \underline{\underline{T}} \underline{z}
   \end{aligned}
   $$

由于 $\dot{\underline{z}} = \underline{\underline{D}} \underline{z}$：找一个 $\underline{\underline{T}}$ matrix，使 $\underline{\underline{T}}^{-1} \underline{\underline{A}} \underline{\underline{T}}$ 满足 $\underline{\underline{D}}$.
We want $\underline{T}$ so that:  

$$
\begin{aligned}
\underline{\underline{T}}^{-1} \underline{\underline{A}} \underline{\underline{T}} &= \underline{\underline{D}} \\
\underline{A} &= \underline{T} \underline{D} \underline{T}^{-1}
\end{aligned}
$$

This is the eigenvalue equation, where:
- columns of $\underline{\underline{T}}$ are the eigenvectors of $\underline{\underline{A}}$
- diagonal elements of $\underline{\underline{D}}$ are eigenvalues of $\underline{\underline{A}}$

$$
\begin{bmatrix}
 & & & \\
 & \underline{\underline{A}} & & \\
 & & &
\end{bmatrix}
=
\begin{bmatrix}
| & | & & | \\
\underline{t}_1 & \underline{t}_2 & \dots & \underline{t}_n \\
| & | & & |
\end{bmatrix}
\begin{bmatrix}
\lambda_1 & & 0 \\
& \lambda_2 & \\
& & \ddots & \\
0 & & & \lambda_n
\end{bmatrix}
\begin{bmatrix}
| & | & & | \\
\underline{t}_1 & \underline{t}_2 & \dots & \underline{t}_n \\
| & | & & |
\end{bmatrix}^{-1}
$$

OR. (without the need to find inverse matrix).

$$
\begin{bmatrix}
 & & & \\
 & \underline{\underline{A}} & & \\
 & & &
\end{bmatrix}
\begin{bmatrix}
| & | & & | \\
\underline{t}_1 & \underline{t}_2 & \dots & \underline{t}_n \\
| & | & & |
\end{bmatrix}
=
\begin{bmatrix}
\lambda_1 & & 0 \\
& \lambda_2 & \\
& & \ddots & \\
0 & & & \lambda_n
\end{bmatrix}
\begin{bmatrix}
| & | & & | \\
\underline{t}_1 & \underline{t}_2 & \dots & \underline{t}_n \\
| & | & & |
\end{bmatrix}
$$

$$
=
\begin{bmatrix}
| & | & & | \\
\lambda_1 \underline{t}_1 & \lambda_2 \underline{t}_2 & \dots & \lambda_n \underline{t}_n \\
| & | & & |
\end{bmatrix}
$$

$\underline{\underline{A}} t_k = \lambda_k \underline{t}_k$.
