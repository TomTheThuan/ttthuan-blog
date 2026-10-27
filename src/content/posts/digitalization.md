---
title: 手写数学笔记转换成电子版
published: 2026-08-07
description: 通过有图像识别功能的AI、markdown、以及matplotlib将纸质笔记电子化的尝试
image: ""
tags:
  - AI
  - 分享
  - 记录
category: 本站记录
draft: false
lang: zh_CN
---

本人比较喜欢把数学这种学科的笔记，用钢笔记在纸上，写着舒服看着也舒服。不过考虑到长久保存和分享需求，有电子版肯定是更方便的。

正好现在AI发展得比较厉害，使用起来也没什么门槛，这里我做了个把纸质版笔记电子化的小尝试：

1. 使用多模态（主要是图像识别，不是OCR成文字再交给LLM的那种）模型识别图片
2. 由模型将图片转换成markdown格式的文件
   - 使用latex block书写数学公式
   - 使用python的matplotlib库绘制图像
3. 以markdown格式放在本博客上（同样也可以储存在本地，obsidian之类的本地知识库中）

直接使用具有多模态的模型有以下优点：

- 根据上下文自动更正识别出错的手写文字
- 将内容输出成规范的markdown格式

---

SKILL：

```txt
---
name: handwritten-math-notes-to-markdown
description: Convert handwritten math notes into Markdown documents that follow the style of a math blog — Markdown base, inline and block LaTeX, Python plotting code as image placeholders, and preserved Chinese-English mixed wording. Use when the user asks to turn handwritten notes, scanned note text, or rough math notes into a formatted Markdown post.
---

# SKILL: 手写数学笔记 → Markdown 转换

## 目标

将手写数学笔记转换为 Markdown 文档:

- Markdown 为基础
- 适当穿插 LaTeX 公式
- 图片位置用 Python 绘图代码占位
- 保留手写笔记原有的中英文混用用词
- 仅在必要时修复语法和拼写错误

---

## 文档结构

- 标题层级从 `##` 开始。文档标题不写成 `#`，直接进入第一个 `##` 小节。
- 小节标题尽量保留笔记原有的措辞，不为了「更正式」而改写。
- 段落之间空一行。不要用多余的分隔线（`---`）打断阅读，除非笔记本身有明显的分段标记。

---

## 公式规范

### 行内公式

短公式用行内 LaTeX：`$...$`

示例：

    这个 rate of change 被称为 derivative 导数：$\dot{N} = \frac{dN}{dt}$

### 独立成行的短公式

如果是单独标出来的、重要的短公式，单独成行，用 `$$...$$`：

    $$
    \dot{N} = kN
    $$

### 长推导

长推导用公式块，可以用 `aligned` 环境对齐：

    $$
    \begin{aligned}
    \frac{dN}{dt} &= kN \\
    \frac{1}{N} \frac{dN}{dt} &= k \\
    \frac{1}{N} \, dN &= k \, dt \\
    \ln N &= kt + C
    \end{aligned}
    $$

### 公式旁注

如果一行公式边上有注释，注释写在公式块的下方，用 bullet point 标出：

    $$
    \dot{x} = ax - bxy
    $$
    
    - $a$：rabbit 自然出生率
    - $b$：被 fox 捕食的速率系数

### 方程组

方程组用 `cases`：

    $$
    \begin{cases}
    \dot{x} = ax - bxy \\
    \dot{y} = cxy - dy
    \end{cases}
    $$

---

## 图片占位规范

笔记中的图（相平面草图、坐标系手绘、示意图等）用一段 Python 绘图代码占位。

- 默认已有 `import numpy as np` 和 `import matplotlib.pyplot as plt`，不需要重复写这两行 import。
- 一般情况下用 numpy 和 matplotlib.pyplot 即可。
- 代码放在 fenced code block 里，语言标为 `python`。
- 代码应尽量还原笔记中那张图想表达的内容（曲线形状、坐标轴含义、关键点标注）。

示例：

    ```python
    t = np.linspace(0, 3, 200)
    N = 1000 * np.exp(1.0 * t)
    plt.plot(t, N)
    plt.xlabel("Time (hours)")
    plt.ylabel("Population N")
    plt.show()
    ```

如果笔记里的图是纯示意、无法用代码还原（比如随手画的箭头框图），用一个简短的文字说明 + 一个最接近的代码骨架占位，不要留空白。

---

## 语言风格规范

### 中英文混用

保持原样，不加任何标记。

- 不加斜体：写 `这个 rate of change 叫做 derivative`，不写 `这个 *rate of change* 叫做 *derivative*`
- 不加代码格式：写 `state variable`，不写 `` `state variable` ``
- 不翻译笔记里已经用的英文术语，也不把中文术语强行换成英文

### 保留原有用词

- 笔记里写 `bacteria` 就写 `bacteria`，不改成 `bacterium` 或「细菌」
- 笔记里写「种群大小」就保留「种群大小」，不改成「population size」
- 不调整语序、不改写句式，除非属于下面的「必要修复」

---

## 必要修复的边界

**只修以下四类：**

1. 明显拼写错误
   - `recieve` → `receive`
   - `seperate` → `separate`

2. 明显语法错误
   - `the bacteria is increase` → `the bacteria increase`
   - `a state variables` → `a state variable`

3. 明显笔误导致的公式错误
   - 比如 `\dot{N} = kN` 写成 `\dot{N} = Kn`，且上下文能确定是笔误

4. 机械性重复标点
   - 因书写习惯在句末多点了一下导致的重复句号：`...变化了。。` → `...变化了。`
   - 英文句末的重复句点：`...change..` → `...change.`
   - 中英文混排时，句末同时出现中英文句号：`...change。.` → `...change。`
   - 中英文混排时，句末如果只有英文句号`.`时：转换成中文句号`。`
   - 判断标准：只看标点本身，重复的、明显是手滑多点的，直接合并成一个；不改变句子内容、不改断句

**不修以下内容：**

- 用词习惯（中式表达、口语化表达）
- 中英文混用的方式
- 句子长短、断句方式
- 术语选择（哪怕和标准译法不同）
- 标点风格（中英文标点混用只要不影响理解就不动）——但注意：机械性重复标点不属于此列，按第 4 类处理

判断标准：**如果改完之后意思变了，或者读起来不像原作者的口气了，就不该改。**

---

## 转换示例

### 手写原文（示意）

    这节讲 state variable。state variable 是描述一个 system 的真实数字，比如 gravity acceleration、当前 object 的位置、一个 population 的大小。
    
    选哪些 state variable 取决于 model 要设计得多复杂。Models are designed to describe a specific aspect of the worlds, while discarding others.
    
    比如 pendulum，可以用垂直夹角 θ 和角速度 ω 来描述当前状态：
    [θ; ω]
    
    如果要考虑 temperature、rotation、pressure 的话，就需要更复杂的 model。

### 转换后

    ## State Variables
    
    State variable 是描述一个 system 的真实数字，这包括：
    
    - 重力加速度
    - 当前物体的位置
    - 一个 population 的大小
    
    具体选择哪些 state variable 取决于一个 model 要设计得多复杂。
    
    Models are designed to describe a specific aspect of the worlds, while discarding others.
    
    例如，对于一个 pendulum 来说，可以用垂直夹角 $\theta$ 和角速度 $\omega$ 来描述当前的状态（如果忽略其他微小的影响因素的话）：
    
    $$
    \begin{bmatrix}
    \theta \\
    \omega
    \end{bmatrix}
    $$
    
    而如果要考虑其他因素，例如温度、旋转、气压…的话，就需要一个更加复杂的 model 来描述。

---

## 检查清单

转换完成后，逐条核对：

- [ ] 标题从 `##` 开始，没有 `#`
- [ ] 短公式行内、重要短公式独立成行、长推导成块
- [ ] 公式旁注写在公式块下方，用 bullet point
- [ ] 图片位置有 Python 绘图代码占位，且未重复写 import
- [ ] 中英文混用保持原样，未加斜体或代码格式
- [ ] 只修了拼写、语法、明显公式笔误
- [ ] 用词、断句、术语选择与手写原文一致
- [ ] 句末没有多余的重复标点（`。。`、`..`、`。.` 等已合并）

```

