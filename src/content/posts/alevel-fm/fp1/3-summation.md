---
title: Further Mathematics - Summation of Series
published: 2026-11-04
description: ''
image: ''
tags: []
category: 'Further Math'
draft: false
lang: 'zh_CN'
---

# Summation of Series 级数求和

This is what '$\sum$ summation' means:
$$
\begin{aligned}
\sum_{r=1}^{n} r &= 1 + 2 + 3 + \dots + n \\
\sum_{r=1}^{n} r^2 &= 1 + 4 + 9 + \dots + n^2 \\
\sum_{r=1}^{n} r^3 &= 1 + 8 + 27 + \dots + n^3
\end{aligned}
$$
## Arithmetic Sequence 等差数列

$$
\begin{aligned}
u_n &= a + (n-1)d \\
S_n &= \frac{n}{2}(2a + (n-1)d)
\end{aligned}
$$

1. $\sum_{r=1}^{n} r$ 的求和（首项加末项除以2）：

$$
\sum_{r=1}^{n} r = \frac{(1+n)n}{2}
$$

2. 一个常数 $k$ 连加 $n$ 次：

$$
\sum_{r=1}^{n} k = nk
$$


- 当 $\sum_{r=1}^{n}$ 变形为 $\sum_{r=1}^{2n}$ 时，$\frac{n(n+1)}{2}$ 中的 $n$ 都换成 $2n$.

$$
\sum_{r=1}^{2n} r = n(2n+1)
$$

- 当起始的数字是$n + 1$而不是$1$时：

$$
\sum_{r=n+1}^{2n} r = \sum_{r=1}^{2n} r - \sum_{r=1}^{n} r
$$

### 例题1

Find $\sum_{r=1}^{n} (4r+1)$, hence determine the value of $\sum_{r=17}^{48} (4r+1)$

$$
\begin{aligned}
\sum_{r=1}^{n} (4r+1) \\
&= 4\sum_{r=1}^{n} r + n & \text{① 拆括号} \\
&= \frac{4n(n+1)}{2} + n & \text{② 套公式} \\
&= 2n^2 + 3n & \text{③ 化简}
\end{aligned}
$$

$$
\sum_{r=17}^{48} (4r+1) = \sum_{r=1}^{48} (4r+1) - \sum_{r=1}^{16} (4r+1)
$$

### 例题2

Find the sum of first $n$ terms of the series $1, 3, 5, \dots$

Method 1: $n$ terms
$$
\begin{aligned}
1+3+5+\dots+n &= \sum_{r=1}^{n} (2r-1) \\
&= 2\sum_{r=1}^{n} r - n \\
&= n^2 + n - n \\
&= n^2
\end{aligned}
$$

Method 2: $2n$ terms in total
$$
\begin{aligned}
1+3+5+\dots &= (1+2+3\dots) - (2+4+6\dots) \\
&= \sum_{r=1}^{2n} r - \sum_{r=1}^{n} (2r) \\
&= \frac{2n(2n+1)}{2} - \frac{2n(n+1)}{2} \\
&= 2n^2 + n - (n^2 + n) \\
&= n^2
\end{aligned}
$$

### 更高次方的series formulae

$$
\begin{aligned}
\sum_{r=1}^{n} r^2 &= \frac{1}{6}n(n+1)(2n+1) \\
\sum_{r=1}^{n} r^3 &= \frac{1}{4}n^2(n+1)^2
\end{aligned}
$$
可以不用背，考试时有 formulae sheet.

## Converging Series 收敛级数

发散的：$\sum = 1+2+3+\dots+n$
收敛的：$\sum = 1+\frac{1}{2}+\frac{1}{3}+\dots+\frac{1}{n}+0$

当 $n \to \infty$ 时，$\sum \to a$

$$
\sum_{r=1}^{n} \frac{1}{r(r+1)} = \frac{1}{r} - \frac{1}{r+1}
$$
其中 $r$ 是最开始的数字.
当 $n \to \infty$，$\sum \to \frac{1}{r}$

将 $\frac{1}{r(r+1)}$ 拆成 $\frac{A}{r} + \frac{B}{r+1}$ 的形式
$$
\begin{aligned}
\frac{1}{r(r+1)} &= \frac{A}{r} + \frac{B}{r+1} \\
1 &= A(r+1) + Br
\end{aligned}
$$
- when $r = -1$, $1 = 0 - B$
  $B = -1$
- when $r = 0$, $A = 1$

得 $\frac{1}{r(r+1)} = \frac{1}{r} - \frac{1}{r+1}$

---

当 $\frac{1}{r(r+n)}$ 中的 $n$ 不为 1 时

$$
\begin{aligned}
\frac{1}{r(r+n)} &= \frac{A}{r} + \frac{B}{r+n} \\
1 &= A(r+n) + Br
\end{aligned}
$$
- when $r = 0$, $A = \frac{1}{n}$
- when $r = -n$, $B = -\frac{1}{n}$

得 $\frac{1}{r(r+n)} = \frac{1}{n}(\frac{1}{r} - \frac{1}{r+n})$

$$
\begin{aligned}
\frac{1}{(r-1)r(r+1)} &= \frac{1}{2}(\frac{1}{(r-1)r} - \frac{1}{r(r+1)}) \\
\frac{1}{m \cdot n} &= \frac{1}{n-m}(\frac{1}{m} - \frac{1}{n})
\end{aligned}
$$
- 其中：$n>m$

- $\frac{1}{m} - \frac{1}{n}$前的系数是 $m$ 和 $n$ 的差

### 例

$$
\begin{aligned}
\sum_{n=1}^{N} \frac{1}{(n+1)(n+2)} &= \sum \frac{1}{1} (\frac{1}{n+1} - \frac{1}{n+2}) \\
&= \sum (\frac{1}{n+1} - \frac{1}{n+2}) \\
&= (\frac{1}{2} - \frac{1}{3}) + (\frac{1}{3} - \frac{1}{4}) + (\frac{1}{4} - \frac{1}{5}) + \dots + (\frac{1}{N+1} - \frac{1}{N+2}) \\
&= \frac{1}{2} - \frac{1}{N+2}
\end{aligned}
$$
when $n \to \infty$, $\frac{1}{2} - \frac{1}{n+2} = \frac{1}{2}$
