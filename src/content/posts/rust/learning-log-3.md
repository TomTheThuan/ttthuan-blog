---
title: Rust学习笔记 3 - Structs and Enums
published: 2026-09-23
description: ''
image: ''
tags: ["rust", "笔记", "计算机"]
category: 'Rust'
draft: false
lang: 'zh_CN'
---

- [Structs](#structs)
  - [Define a Struct](#define-a-struct)
  - [Basic Usages](#basic-usages)
  - [Debug](#debug)
  - [Implement Methods for Struct](#implement-methods-for-struct)
- [Enums](#enums)
  - [Enum with Implemented Function](#enum-with-implemented-function)
  - [Option Type](#option-type)

## Structs

### Define a Struct

定义带field name的struct：

```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}
```

定义无field name的struct：

```rust
struct Color(i32, i32, i32);  // 近似为带名字的tuple
```

### Basic Usages

使用带field name的struct：

```rust
let mut user1 = User {
    active: true,
    username: String::from("someusername123"),
    email: String::from("someone@example.com"), 
    sign_in_count: 1,
};
println!("The username of user1 is: {user1.username}")
```

复制一个struct到新的struct实例：

```rust
let user1_copy = User {
    active: user1.active,
    username: user1.username,
    email: String::from("anotheremail@example.com"),
    sign_in_count: user1.sign_in_count,
}
// 可以将上述代码化简成以下的形式，算是一种比较好用的语法糖
let user1_copy = User {
    email: String::from("anotheremail@example.com"),
    ..user1  // must be placed at the end
};
```

在function中使用struct：

```rust
fn build_user(username: String, email: String) -> User {
    User {
        active: true,
        username: username,
        email: email,
        sign_in_count: 1,
    }
}
```

在rust中有一种"field init shorthand"的annotation，可以用来很方便地构建一个struct：

```rust
fn build_user(username: String, email: String) -> User {
    User {
        active: true,
        username,
        email,
        sign_in_count: 1,
    }
}
```

> 将`username: username,`直接化简成`username,`

就算不同struct内部的数据是一样的，二者还是不一样的：

```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);
fn main() {
    let black = Color(0, 0, 0);
    let origin = Point(0, 0, 0);
}
```

就像重新解析tuple一样，在指定struct的类型时，也可以重新解析一个struct：

```rust
let a = Point(1, 2, 3);
let Point(x, y, z) = a;
println!("x: {x}, y: {y}, z: {z}");
```

### Debug

要把一个struct中的内容输出到console，需要在定义这个struct时启用`Debug`标签：

```rust
#[derive(Debug)]  // add debug label to enable printing
struct Rectangle {
    width: u32,
    height: u32,
}
```

然后在`println！()`中就可以通过`:?`或`:#?`打印了：

```rust
println!("rect1 is {rect1:?}");  
println!("rect1 is {rect1:#?}"); 
```

输出结果为：
```
rect1 is Rectangle { width: 60, height: 50 }
rect1 is Rectangle {
    width: 60,
    height: 50,
}
```

关于debug，有个宏`dbg!()`也非常好用：

> `dbg!` 宏接收一个表达式的所有权（与 `println!` 宏相反，后者接收的是引用），打印出代码中调用 dbg! 宏时所在的文件和行号，以及该表达式的结果值，并返回该值的所有权。

```rust
let rect1 = Rectangle {
    width: dbg!(30 * scale),
    height: 50,
};
dbg!(&rect1);
```

这个地方调用了两次`dbg!()`，输出为：

```
[src/main.rs:36:16] 30 * scale = 60
[src/main.rs:41:5] &rect1 = Rectangle {
    width: 60,
    height: 50,
}
```

### Implement Methods for Struct

添加方法时的基础语法：

```rust
impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}
impl Rectangle {
    fn square(size: u32) -> Self {
        Self {width: size, height: size}
    }
}
```

- 一个struct可以有多个`impl`块，块内的function被称为“associated function”

- 在`impl`中，第一个参数为`&self`的function被称为“method”

  ```rust
  fn main() {
      let rect1 = Rectangle {
          width: 60, 
          height: 50,
      };
      println!(
          "The area of the rectangle is {} square pixels.",
          rect1.area()
      );
  }
  ```

- 不是method的associated function通常被用于当作constructor

  ```rust
  dbg!(Rectangle::square(30));
  ```

  ```
  [src/main.rs:64:5] Rectangle::square(30) = Rectangle {
      width: 30,
      height: 30,
  }
  ```

## Enums

基础语法：

```rust
enum IpAddrKind {
    V4,
    V6,
}

fn route(ip_kind: IpAddrKind) {}  // restrict the type

// Usages
let four = IpAddrKind::V4;
let six = IpAddrKind::V6;

route(IpAddrKind::V4);
route(IpAddrKind::V6);
```

将enum和strcut组合使用：

```rust
enum IpAddrKind {
    V4,
    V6,
}

struct IpAddr {
    kind: IpAddrKind,
    address: String,
}

// Usages
let home = IpAddr {
    kind: IpAddrKind:: V4,
    address: String::from("127.0.0.1"),
};
let loopback = IpAddr {
    kind: IpAddrKind::V6,
    address: String::from("::1"),
};
```

为了在使用enum存有限个状态的同时，添加与这个状态相关的数据，rust允许使用一种更高级的语法：

```rust
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

// Usages
let home = IpAddrSimple::V4(127, 0, 0, 1);
let loopback = IpAddrSimple::V6(String::from("::1"));
```

### Enum with Implemented Function

```rust
enum Message {  // 为每个不同的枚举状态关联不同的类型和值
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}

impl Message {
    fn call(&self) {
        println!("Call...");
        match self {
            Message::Quit => println!("Quit"),
            Message::Move{x, y} => println!("Move ({x}, {y})"),
            Message::Write(a) => println!("Write: {a}"),
            Message::ChangeColor(a, b, c) => println!("Change Color: {a}, {b}, {c}"),
        }
    }
}

fn main() {
    let m = Message::ChangeColor(1, 2, 3);
    m.call();
    let m = Message::Write(String::from("Hello, world!"));
    m.call();
    let m = Message::Move{ x: 30, y: 50};
    m.call();
    let m = Message::Quit;
    m.call();
}
```

```
Call...
Change Color: 1, 2, 3
Call...
Write: Hello, world!
Call...
Move (30, 50)
Call...
Quit
```

Enum上可以实现方法。

### Option Type

```rust
// Use the build-in enum type Option<T>
// <T> 是一个泛类型参数。
let some_number = Some(5);
let some_char = Some('e');
let absent_number: Option<i32> = None;  // tell the compiler the data type of `None` is `i32`
```

rust中，通常使用Option来处理空值，在使用Option定义空值时，仍然要求提供这个空值的数据类型。
