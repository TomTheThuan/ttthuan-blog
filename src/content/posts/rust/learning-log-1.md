---
title: Rust学习笔记 1 - Basic Concepts
published: 2026-09-18
description: ''
image: ''
tags: ["rust", "笔记", "计算机"]
category: 'Rust'
draft: false
lang: 'zh_CN'
---

> [!IMPORTANT]
>
> 不要把这个blog当作教程来看，这个blog对我来说只是一个方便回顾的quick reference，里面不会包含完整的代码。
>
> 如果要教程或源码的话去官方Rust Book网站获取：[The Rust Programming Language](https://doc.rust-lang.org/book/title-page.html)

- [Installation](#installation)
- [Hello Rust](#hello-rust)
  - [Hello World](#hello-world)
  - [Hello Cargo](#hello-cargo)
  - [Quick Glance into Rust: Guessing Game Demo](#quick-glance-into-rust-guessing-game-demo)
- [Common Programming Concepts](#common-programming-concepts)
  - [Numbers](#numbers)
  - [Boolean and Character](#boolean-and-character)
  - [Compound Types](#compound-types)
- [Functions](#functions)
- [Control Flow](#control-flow)
  - [IF statement](#if-statement)
  - [LOOP](#loop)

## Installation

本人环境`Arch Linux`：`sudo pacman -S rustup`

> 这个机器上已经有rust了，rust和rustup包冲突，选择rustup

安装完rustup之后，使用rustup安装rust最新版：

```shell
$ RUSTUP_DIST_SERVER=https://mirrors.tuna.tsinghua.edu.cn/rustup rustup install stable  # 使用tuna镜像
```

编辑shell配置文件`.zshrc`，以便日后使用tuna镜像更新rust：

```shell
# Rust Mirror
# https://mirrors.tuna.tsinghua.edu.cn/help/rustup/
export RUSTUP_UPDATE_ROOT=https://mirrors.tuna.tsinghua.edu.cn/rustup/rustup
export RUSTUP_DIST_SERVER=https://mirrors.tuna.tsinghua.edu.cn/rustup
```

编辑`~/.cargo/config.toml`，配置镜像：

```toml
[source.crates-io]
replace-with = 'mirror'

[source.mirror]
registry = "sparse+https://mirrors.tuna.tsinghua.edu.cn/crates.io-index/"

[registries.mirror]
index = "sparse+https://mirrors.tuna.tsinghua.edu.cn/crates.io-index/"
```

下载EPUB版中文Rust Book：

```shell
$ git clone git@github.com:KaiserY/trpl-zh-cn.git
$ cd trpl-zh-cn
$ cargo run --release --manifest-path epub-builder/Cargo.toml
```

这样我一般学习的话就看生成的`rust_programming_language.epub`来学习，遇到一些术语之类的单词再回头去翻英文原版。

如果为了完全离线使用这本书，建议下载这本书所需的一些依赖：

```shell
$ cargo new get-dependencies
$ cd get-dependencies
$ cargo add rand@0.8.5 trpl@0.2.0
```

> 建一个空项目，并缓存这些包，日后要在其他项目中使用的时候就不需要重复下载了。

## Hello Rust

我新建了一个叫`learn-rust`的目录，专门存放学习rust时产生的各种文件：`mkdir ~/Documents/learn-rust`

### Hello World

在`learn-rust`目录中：

```shell
$ mkdir hello_world
$ cd hello_world
```

> rust文件一般用snake_case命名。

新建源码文件：

```shell
$ vim main.rs
```

输入代码：
```rust
// main.rs
fn main() {
    println!("Hello, world!");
}
```

保存文件后，编译并运行程序：
```shell
$ rustc main.rs
$ ./main
Hello, world!
```

---

- rust代码中的表达式用`;`分割（绝大多情况下用`;`结尾）
- rust代码的入口在`main()`函数中

### Hello Cargo

Cargo是rust官方推的出、所谓的“一站式”管理器，可以用来构建系统和管理依赖。

确认是否安装Cargo：`cargo --version`

回到`learn-rust`目录：

```shell
$ cargo new hello_cargo
$ cd hello_cargo
```

在`hello_cargo`项目中：

```
.
├── Cargo.lock	# 用来储存依赖状态
├── Cargo.toml	# 用于储存项目配置
├── src/		# 存放源码的
└── target/		# 用于存放cargo生成的一些文件
```

Cargo默认将代码放在`src`目录中，并且以`main.rs`作为入口文件。

使用Cargo编译可执行文件：在项目根目录下执行`cargo build`

或者说可以直接使用`cargo run`来运行编译后的文件（如果没有最新版的编译文件的话则会先进行编译），一般情况下用`cargo run`能覆盖绝大多数情况了。

如果要检查语法而不是编译文件的话，可以使用`cargo check`。这个命令个人认为也很常用。

> 当要发布文件时，请使用`cargo build --release`来编译文件。`--release`选项会在编译时优化程序，让最后生成的程序在运行时效率更高，但代价是更长的编译时间。

### Quick Glance into Rust: Guessing Game Demo

新建项目：

```shell
$ cargo new guessing_game
$ cd guessing_game
```

编辑`Cargo.toml`，添加依赖：

```toml
[package]
name = "guessing_game"
version = "0.1.0"
edition = "2024"

[dependencies]
rand = "0.9.3"  # 用于生成随机数
```

---

编辑`src/main.rs`中的代码：

引入external libraries：

```rust
use std::cmp::Ordering;	// 用于比较数字大小
use std::io;  			// use standard io library，用于处理标准输入
use rand::Rng;			// 用于生成随机数
```

生成随机数，并储存进变量中：

```rust
fn main () {
   // ...
   let secret_number = rand::rng().random_range(1..=100);
   // ...
}
```

在rust中，变量默认**不可变**，而rust中也有常量这一个改变，这区别在于：

- 常量：在编译时可以确定具体的值是什么，例如一个数字或一个字符串字面量
- 不可变变量：在变量创建之后不可以更改里面的值

---

获取用户输入：

```rust
println!("Please input your guess:");
let mut guess = String::new();

io::stdin()
    .read_line(&mut guess)
    .expect("Failed to read line");

let guess: u32 = match guess.trim().parse() {
    Ok(num) => num,
    Err(_) => continue,
};

println!("You guessed: {guess}");
```

`let mut guess = String::new();`会创建一个可变的guess变量，由于现在只能知道这个变量的类型是`String`，具体是什么值并不清楚，作用使用`String::new()`新建一个空的`String`对象。

这两句是等价的（目前看来）：

```rust
let mut guess = String::new();                
let mut guess = String::from("");
```

读取用户输入并存进`guess`变量中：

```rust
io::stdin()
    .read_line(&mut guess)
    .expect("Failed to read line");
```

在Rust中经常使用chain call来实现逻辑，这样比使用一堆相互嵌套的functions要简洁得多。`&mut guess`将`guess`这个可变变量的内存地址传给`read_line()`，`read_line()`会把用户的输入存进这个地址中（也就是存进这个变量），`expect()`则是用来处理错误时的报错信息。

尝试将用户的输入转换成`u32`类型（占4个byte的无符号int）：

```rust
let guess: u32 = match guess.trim().parse() {
    Ok(num) => num,
    Err(_) => continue,
};
```

`guess.trim()`会移除收尾的空格，`parse()`会接着尝试将处理完空格的字符串转换成`u32`格式。

- 如果转换成功，则返回`Ok(num)`，`num`是处理后的数字
- 当发生错误时返回`Err()`，里面的是发生的错误的报错信息。通配符`_`会匹配所有类型的信息，由于以上这段代码在一个loop中运行，所以`continue`相当于跳过这次输入，直接进入下一个iteration的开始。

---

比较数字：

```rust
match guess.cmp(&secret_number) {              
    Ordering::Less => println!("Too small!"),  
    Ordering::Greater => println!("Too big!"), 
    Ordering::Equal => {                       
        println!("You win!");                  
        break;                                 
    }                                          
}                                              
```

调用`cmp()`比较数字，传入`secret_number`的内存地址（也就是`&secrete_number`），返回一个`Ordering`（enum对象）的值。并用match语句处理这些结果。

## Common Programming Concepts

定义并使用常量：

```rust
const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;
println!("3 hours = {THREE_HOURS_IN_SECONDS} seconds");
```

定义和使用变量：

```rust
let mut x = 5;                                                                            
println!("The vaule of x is {x}");                                                        
{                                                                                         
    let x = x * 2;  // Declare x again in inner scope, it will not affect the outer scope 
    println!("The value of x in inner scope is {x}");                                     
}                                                                                         
x = x + 1;                                                                                
println!("The vaule of x is {x}");
```

在上面`{}`中定义的`x`不会影响到外部`x`的值，内部`x`的scope只在`{}`内。

重复定义一个变量被称为shadowing，shadowing允许用同一个变量名处理同系列不同类型的数据。

```rust
let spaces = "        ";               
let spaces = spaces.len();             
println!("There are {spaces} spaces"); 
```

### Numbers

**Integer**的类型：

| length   | has symbol | no symbol |
| -------- | ---------- | --------- |
| 8-bit    | `i8`       | `u8`      |
| 16-bit   | `i16`      | `u16`     |
| 32-bit   | `i32`      | `u32`     |
| 64-bit   | `i64`      | `u64`     |
| 128-bit  | `i128`     | `u128`    |
| arch-dep | `isize`    | `usize`   |

> Symboled integer uses **two's complement** to represent

| 数字字面值                    | 例子          |
| ----------------------------- | ------------- |
| Decimal（十进制）             | `98_222`      |
| Hex（十六进制）               | `0xff`        |
| Octal（八进制）               | `0o77`        |
| Binary（二进制）              | `0b1111_0000` |
| Byte（字节字面值，仅限 `u8`） | `b'A'`        |

```rust
println!("Different representations of ints: {} {} {} {} {}",
    98_222, 0xff, 0o77, 0b1111_0000, b'A'
);
```

Float类型：`f32`和`f64`，所有float类型都是带符号的。

```rust
let x = 2.0; 		// 无特殊说明时默认64位（f64）
let y: f32 = 3.0; 	// f32
```

数字运算：

```rust
println!("sum: {}", 5 + 10);            
println!("difference: {}", 95.5 - 4.3); 
println!("product: {}", 4 * 30);        
println!("quotient: {}", 56.7 / 32.2);  
println!("quotient 2: {}", -5.0 / 3.0); 
println!("truncated: {}", -5 / 3);      
println!("remainder: {}", 43 % 5);      
```

`5.0 / 3.0`和`-5 / 3`的区别在于：

- 带`.0`的`-5.0 / 3.0`本质是float类型，所以计算出的结果包含小数点
- 不带`.0`的`-5 / 3`本质上是int类型，所以计算出的结果会被“截断”，只取整数部分

### Boolean and Character

```rust
// Boolean                                                       
let t = true;                                                    
let f: bool = false;                                             
println!("t: {t} f: {f}");                                       
                                                                 
// Character                                                     
let c = 'z';  // char uses single quotation mark                 
let z: char = 'Z';                                               
let heart_eyed_hachimi = '😻';  // char costs 4 bytes in default 
println!("{c} {z} {heart_eyed_hachimi}");                        
```

Boolean没什么值得多说的。

单引号`'`表示`char`类型，一个`char`默认默认使用4 Bytes，为了储存Unicode字符（Unicode Scalar Value）。

### Compound Types

这个复合类型组要包括的是tuple和array。

```rust
let tup: (i32, f64, u8) = (500, 6.4, 1);     
let (x, y, z) = tup;                         
println!("The value of y is {y}");           
println!("First element in tup: {}", tup.0); 
```

一个tuple中每个element的类型不要求一样。

`let (x, y, z) = tup;`可以把一个tuple中的值重新解析到多个变量上。

通过`tuple.index`（e,g, `tup.0`）的方式可以通过index获取一个tuple element的值。

```rust
let array = [1, 2, 3, 4, 5];
let index = 3;
let element = array[index];
println!("The value of the element at index {index} is {element}");

let array: [i32; 5] = array;
let array = [3; 5];
println!("First element in array: {}", array[0]);

```

访问array中element的语法是`array[index]`，和其他的语言没什么区别。

**明确指出**一个array的类型和长度：`let array: [i32; 5] = [1, 2, 3, 4, 5];`

快速生成一个包含重复元素的array：`let array = [init_val; length];`，例如`[3; 5]`等同于`[3, 3, 3, 3, 3]`

> 通过读取用户输入的index值来访问array：
>
> ```rust
> let array = [1, 2, 3, 4, 5];
> println!("Please enter an array index:");                          
> let mut index = String::new();                                     
> io::stdin()                                                        
>     .read_line(&mut index)                                         
>     .expect("Failed to readline");                                 
> let index: usize = index                                           
>     .trim()                                                        
>     .parse()                                                       
>     .expect("Index entered was not a number");                     
> let element = array[index];                                        
> println!("The value of the element at index {index} is {element}");
> ```
>
> 如果用户输入的index超出了array的范围，rust会产生一个runtime error，并立即退出，防止程序像c语言那样访问到不该访问的内存信息。

## Functions

调用函数：

```rust
fn main() {
    another_function();
    print_labeled_measurement(5, 'h');
    let mut x = five();
    x = plus_one(x);
    println!("The value of x is {x}");
}

fn another_function() { println!("Another Function"); }
fn print_labeled_measurement(val: i32, unit: char) {
    println!("The measurement is: {val}{unit}");
}
fn five() -> i32 { 5 }
fn plus_one(x: i32) -> i32 { x + 1 }
```

这部分没什么特殊的内容。

Rust是一个“基于表达式”（expression-based）的语言，所以可以为一个变量赋予一组表达式：

```rust
let y = {                      
    let x = 3;                 
    x + 1  // must not use `;` 
};
```

`{}`中的`x + 1`是一个表达式，这个表达式在`{}`的末尾，这个表达式的值会以类似于“返回值”之类的方式，重新赋予到调用这个表达式的变量上。

> [!IMPORTANT]
>
> 如果使用`x + 1;`那么这就变成一个语句了。

关于语句statement和表达式expression之间的区别，类似于procedure和function之间的区别（是否产生返回值）：

- **语句**（*Statements*）是执行一些操作但不返回值的指令。
- **表达式**（*Expressions*）计算并产生一个值。

函数末尾的表达式会自动作为这个函数的返回值返回，同样也可以用`return xxx`的方式在函数中直接返回返回值。

> [!IMPORTANT]
>
> rust中赋值语句不会返回任何值，所以不能写`x = y = 1`这种“连锁”赋值。

## Control Flow

### IF statement

```rust
let number = 8;                                                                 
if number < 5 {                                                                 
    println!("less than 5");                                                    
} else if number == 5 {                                                         
    println!("equal to 5");                                                     
} else {                                                                        
    println!("greater than 5");                                                 
}                                                                               
                                                                                
let condition = true;                                                           
let number = if condition { 5 } else { 10 };
println!("The value of number is {number}");                                    
```

由于rust是expression-based的语言，所以if也可以直接所谓运算符使用，而不像python中专门需要`value = "true" if condition else "false"`。

rust中的`value = if condition { "true" } else { "false" }`更符合直觉，扩展性也更强。

### LOOP

WHILE循环：

```rust
// 生成第 n 个斐波那契数           
let mut counter = 1;               
let n = 7;                         
let mut pre = 0;                   
let mut result = 1;                
while counter < n {                
    if counter == n {              
        break;                     
    }                              
    let tmp = result;              
    result += pre;                 
    pre = tmp;                     
    counter += 1;                         
};                                 
println!("The result is {result}");
```

`loop`语句：

```rust
let mut counter = 0;
let result = loop {
    counter += 1;
    if counter == 10 {
        break counter * 2;
    }
};
```

loop循环相当于python中的`while True:`用法。

`break`语句在loop中相当于函数中`return`的用法。

**Loop labeling**

```rust
// loop labeling             
let mut i = 0;               
'loop_i: loop {              
    let mut j = 0;           
    'loop_j: loop {          
        println!("{i} {j}"); 
        if i == 5 {          
            break 'loop_i;   
        };                   
        if j == 3 {          
            break;           
        };                   
                             
        j += 1               
    };                       
    i += 1;                  
};                           
```

使用loop labeling语法时，`break`可以选择从哪个loop退出。默认情况下loop是退出最内侧的循环。

FOR循环：

```rust
let a = [10, 20, 30, 40, 50];
for element in a {
    println!("The value is {element}");
};
                                                     
for number in (1..4).rev() {  // (1..4) is [1, 2, 3] 
    println!("{number}!");
};                                                   
```

for循环可以直接作用在iterable的对象上，例如array。

`(1..4)`是range的语法，在数学上的表示方式则是$[1, 4)$，`assert_eq!((1..4), [1, 2, 3]);`
