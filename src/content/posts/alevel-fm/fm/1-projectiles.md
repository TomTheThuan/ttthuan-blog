---
title: Further Mechanics - Projectiles [Preview Sample]
published: 2026-07-01
description: ''
image: ''
tags: []
category: 'Further Math'
draft: false
lang: 'zh_CN'
---

# Projectile
- Newton's equations of motion and projectile motion.
- Projectiles from both ground level and raised platforms.
- component of motion

## Projectile

$u$ 可以被拆解成两个分力: Horizontal: $u_x = u\cos\theta$ ; vertical: $v_y = u\sin\theta$

其中 $u_x$ 不随改变，$u_x$ 没有受到任何加速度，
所以 $u_x = u\cos\theta$ （$u_x$ 永远是初速度 $u$ 在 $x$ 方向上的水平分量）

其中 $u_y$ 会因为 $g$ 的存在而不断随时间变化，
所以 $v_y = u\sin\theta - gt$ （由 $v = u + at$ 推导得，$a$ 为负数，$g$ 为重力加速度，故 $a = -g$）

---

当 particle 位于最高点时，垂直分量为 0，所以：
$v_y = 0$ when the particle reaches its highest point.

$$
v_y = u\sin\theta - gt
$$
$$
0 = u\sin\theta - gt
$$
$$
t = \frac{u\sin\theta}{g} \quad \rightarrow \text{到达最高点的时间}.
$$

一个 particle 从出发点再到最高点，再从最高点回到与出发点同一水平线
时的时间是一样的，所以：
the total flight time is $t = \frac{2u\sin\theta}{g}$

---

将 $\begin{cases} u_x = u\cos\theta \\ u_y = u\sin\theta \end{cases}$ (分量) 应用到 $s = ut + \frac{1}{2}at^2$， 得到：
$$
x = u\cos\theta \cdot t
$$
$$
y = u\sin\theta \cdot t - \frac{1}{2}gt^2
$$

将 total flight time $t = \frac{2u\sin\theta}{g}$ 应用到 $x = u\cos\theta \cdot t$ 时：
$$
x = \frac{u^2\sin2\theta}{g} \quad \rightarrow \text{水平路径 (起点和终点在同一水平线)}.
$$

例：
$u = 20\ \text{m/s}$
$\theta = 50^\circ$
1. 求 range of the particle.
2. 求 the time taken to reach the highest point.
3. 求 the speed of the particle when $t = 0.5\ \text{s}$.

1. 
$$
x = \frac{u^2\sin2\theta}{g}
= \frac{20^2\sin100^\circ}{10}
= 30.6\ \text{m}
$$

2. 
$$
t = \frac{u\sin\theta}{g}
= \frac{20\sin50^\circ}{10}
= 0.8455\ \text{s}
$$

3. 
$$
v_x = u\cos\theta = 20\cos50^\circ = 15.126
$$
$$
v_y = u\sin\theta - gt = 20\sin50^\circ - 5 = 3.452.
$$
$$
v = \sqrt{v_x^2 + v_y^2}
= 18.452\ \text{m/s}
$$

---

另外用 $v^2 = u^2 + 2as$ 求 particle 在某一高度时的垂直速度：
$$
v^2 = u^2 + 2as \quad \text{其中 } s \text{ 为垂直方向位移变化量}.
$$
$$
v_y^2 = u_y^2 - 2gs
$$
$$
v_y^2 = (u\sin\theta)^2 - 2gs
$$

---

例1：

1. 求 the exact time taken to reach the floor.
2. 求 the exact speed as it hits the floor.

1. Time.
$$
s = ut + \frac{1}{2}at^2
$$
$$
-15 = 25\sin45^\circ \cdot t - 5t^2
$$
$$
0 = t^2 - \frac{5}{2}\sqrt{2}\ t - 3
$$
$$
t = \frac{\frac{5}{2}\sqrt{2} \pm \sqrt{\frac{25}{2} + 12}}{2}
$$
$t > 0$
$$
t = 3\sqrt{2}
$$
The particle takes $3\sqrt{2}\ \text{s}$ to reach the floor.

2. Speed.
① $v^2 = u^2 + 2as$
$$
v_y^2 = (25\sin45^\circ)^2 + 20\times15
$$
$$
v_y^2 = 612.5
$$
$$
v_y = 17.5\sqrt{2}.
$$
② $v_x = u\cos\theta = 12.5\sqrt{2}.$
③ $v = \sqrt{v_x^2 + v_y^2}$
$= 5\sqrt{37}\ \text{m/s}$

The speed is $5\sqrt{37}\ \text{m/s}$

同样的方法还可以应用到负角度上：

$10\ \text{m/s}$
$20^\circ$ ← 初始角 这种情况，求这个 particle 做 抛物线运动，下降 900m 之后的时速。

①. Vertically:
$$
v^2 = u^2 + 2as
$$
$$
v_y^2 = (10\sin(-20^\circ))^2 + 20\times900
$$
$$
v_y^2 = 18017.88006...
$$
$$
v_y = 134.23
$$

②. Horizontally: $v_x = 10\cos20^\circ = 9.06.$
③. $v = \sqrt{v_x^2 + v_y^2}$
$= 134.54$

## The Cartesian equation of the trajectory
Trajectory: 发射角度, 发射速度, 水平位移, 垂直位移 四者之间的关系
$\theta$  $u$  $x$  $y$.

已知: 

$$
s = \frac{1}{2}(u+v)t \\
x = \frac{1}{2}(u\cos\theta + u\cos\theta)t \\
x = u\cos\theta \cdot t  \\
t = \frac{x}{u\cos\theta}
$$

再将 $t = \frac{x}{u\cos\theta}$ 代入进 $y = u\sin\theta \cdot t - \frac{1}{2}gt^2$ 中：

$$
y = u\sin\theta\left(\frac{x}{u\cos\theta}\right) - \frac{1}{2}g\left(\frac{x}{u\cos\theta}\right)^2 \\
= u\sin\theta\left(\frac{x}{u\cos\theta}\right) - \frac{1}{2}g\left(\frac{x^2}{u^2\cos^2\theta}\right)  \\
\boxed{y = x\tan\theta - \frac{gx^2}{2u^2\cos^2\theta}}
$$

以此为基点, 一个 particle 在发射后再次回到同一水平线的情况, 可以假设 $y=0$ 推导出来:
$0 = x\left(\tan\theta - \frac{gx}{2u^2\cos^2\theta}\right)$
$\tan\theta = \frac{gx}{2u^2\cos^2\theta}$
$x = \frac{u^2\sin2\theta}{g}$ distance

这与之前推导的 total flight time 的式子相同.

例:
一个 particle 在高度为1的地方发射, $\theta = 25^\circ$, $u = 20$, 求下降时高度为2时 particle 经过的水平距离.
解: 高度变化为 $1(= 2-1)$
$y = x\tan\theta - \frac{gx^2}{2u^2\cos^2\theta}$
$1 = \tan25^\circ x - \frac{10}{800\cos^225^\circ}x^2$
$\begin{cases} x \approx 2.32 \\ x \approx 25.3 \end{cases}$
And, the horizontal distance is 25.3 m.

---

当已知一个 parabolic path (例如 $y = 3x - \frac{1}{6}x^2$) 时, 可以反推出
该 particle 的初速度和发射角度.
$u$  $\theta$

例: 已知 $y = 0.3x - 0.1x^2$, 求 $u$ 和 $\theta$.
$\because y = x\tan\theta - \frac{gx^2}{2u^2\cos^2\theta}$
$y = 0.3x - 0.1x^2$
$\therefore \begin{cases} \tan\theta = 0.3 \quad \text{①} \\ \frac{g}{2u^2\cos^2\theta} = 0.1 \quad \text{②} \end{cases}$

①. $\tan\theta = 0.3$
$\begin{cases} \theta = 16.7^\circ & \text{向上的 } 16.7^\circ, \text{ 保留.} \\ \theta = -163.3^\circ & \text{向下的 } 163.3^\circ, \text{ reject.} \end{cases}$

②. $u^2 = \frac{g}{2\times0.1\times\cos^2\theta}$
$u^2 = \frac{10}{0.2}$
$u = 7.35$
Ans: $\theta = 16.7^\circ$, $u = 7.35$

通过一个 parabolic path, 反推出 particle 在某一角度的其他状态.
例1:
Initial Condition: $y_0 = 50$, $u = 25$, $a = -10$
Unknown Condition: $\theta = -30^\circ$
解:
$\begin{cases} v_x = 25\cos30^\circ \\ v_y = 25\sin30^\circ \end{cases}$  $\tan(-30^\circ) = \frac{dy}{dx}$
$-v_y = \frac{\sqrt{3}}{3}v_x$
①. $v = u + at$
$v_y = 25\sin10^\circ - gt$
$-\frac{\sqrt{3}}{3}v_x = 25\sin10^\circ - gt$ } 错[?]转换
$-\frac{\sqrt{3}}{3}\times25\cos30^\circ = 25\sin10^\circ - gt$  ← $v_x$ 不在[?]时改变
$t = 9.673$

②. $s = ut + \frac{1}{2}at^2$
$y = 25\sin10^\circ - 5t^2$
$= -9.14$
③. $50 - 9.14 = 40.9$ m. above the ground.

---

## Summary
初始速度的分量 $\begin{cases} u_x = u\cos\theta \\ u_y = u\sin\theta \end{cases}$

考虑时间 $t$ 和加速度 $a$ 后得到: 应用 $v = u + at$.
$\begin{cases} v_x = u\cos\theta &, a = 0 \\ v_y = u\sin\theta - gt &, a = -g \end{cases}$

将速度与位移建立联系: 应用 $s = ut + \frac{1}{2}at^2$.
$\begin{cases} x = u\cos\theta \cdot t \\ y = u\sin\theta \cdot t - \frac{1}{2}gt^2 \end{cases}$  → 特例: total flight time $t = \frac{2u\sin\theta}{g}$

将上两个式子结合, 可以消除 $t$, 得到
初速度(发射角度 $\theta$ 和发射速度 $u$) 和位移(水平方向 $x$ 和垂直方向 $y$)
的关系式:
$y = x\tan\theta - \frac{gx^2}{2u^2\cos^2\theta}$

或公式表的写法:
$\boxed{y = x\tan\theta - \frac{gx^2}{2u^2\cos^2\theta}}$ → 这个式子同样可以推出 $y=0$ 时 $x = \frac{u^2\sin2\theta}{g}$
总经过的水平距离 (range)

上式 $x$ 项的系数为 $\tan\theta$, $x^2$ 项的系数为 $-\frac{g}{2u^2\cos^2\theta}$, 则
当 $y = ax - bx^2$ 时:
$\begin{cases} a = \tan\theta \\ b = \frac{g}{2u^2\cos^2\theta} \end{cases}$

$\theta$: Angle of projection: $\boxed{\tan\theta = \frac{v_y}{v_x}}$
