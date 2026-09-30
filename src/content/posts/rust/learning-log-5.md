---
title: Rust学习笔记 5 - Crates
published: 2026-09-29
description: ''
image: ''
tags: ["rust", "笔记", "计算机"]
category: 'Rust'
draft: false
lang: 'zh_CN'
---

Crate在rust中相当于别的语言中的library，方便以模块化的方式管理代码（包括组织代码，分享代码等）。

重要概念：

- **Packages** - Cargo的一个功能，可以用来构建、测试和分享crate
- **Crate** - 一个模块树，可以想象成一个包含了很多模块的文件夹（文件树）
- **Modules** - 最基本的一个模块单位
- **Path** - 用于定位一个模块或模块内部某个物体的方式

一个crate是rust编译器每次处理的最小代码单位，也就是说能够被完整编译的最小单位。有两种crate：

- **Binary Crate** - 这是可执行程序，包含一个`main`函数来定义程序在执行时的行为。直接编写，并可以通过`cargo run`执行的rust程序，就属于*binary crate*

  ```shell
  $ cargo new my-project
  my-project
  ├── Cargo.lock
  ├── Cargo.toml
  └── src
      └── main.rs
  ```

  默认包含`src/main.rs`

- **Library Crate** - 这是一个library（在其他编程语言的概念中）。一般crate指的是library crate

  ```shell
  $ cargo new my-library --lib
  my-library
  ├── Cargo.lock
  ├── Cargo.toml
  └── src
      └── lib.rs
  ```

  默认包含`src/lib.rs`

## 创建一个Library Crate

创建一个名为`restaurant`的crate：

```shell
$ cargo new restaurant --lib
```

在`src/lib.rs`中通过`mod`关键字来创建一个module：

```rust
// src/lib.rs
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}

        fn seat_at_table() {}
    }

    mod serving {
        fn take_order() {}

        fn serve_order() {}

        fn take_payment() {}
    }
}
```

这段代码定义了一个叫作`front_of_house`的模块，`front_of_house`模块内部有两个子模块：`hosting`和`serving`。

> `src/main.rs` 和 `src/lib.rs` 叫做 crate 根。之所以这样叫它们是因为这两个文件的内容都分别在 crate 模块结构的根组成了一个名为 `crate` 的模块，该结构被称为**模块树**（*module tree*）。

此时这个crate的模块树是：

```
crate
 └── front_of_house
     ├── hosting
     │   ├── add_to_waitlist
     │   └── seat_at_table
     └── serving
         ├── take_order
         ├── serve_order
         └── take_payment
```

## 使用Modules

重点关键字：

- `pub` - 一个模块中的东西默认是private的，也就是说外部的代码不可以访问一个crate内部的东西。外部需要访问一个模块的话，这个模块需要使用`pub`作为公有模块。
- `use` - 在rust中可以通过指定一个module的path的方式来使用一个module，`use`可以将一个module中的代码带到当前scope中，这样就不用每次指定path了。

创建一个可以被外部使用的模块：

```rust
// src/lib.rs
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}
pub fn eat_at_restaurant() {}
```

上述代码创建了一个叫`front_of_house`的模块（位于crate根下），`front_of_house`下有一个公有的`hosting`模块。

一个可以被外部使用的`eat_at_restaurant`函数同样位于crate根下。

在`eat_at_restaurant`使用`add_to_waitlist`的方式：

1. 使用Absolute Path

   ```rust
   pub fn eat_at_restaurant() {
       crate::front_of_house::hosting::add_to_waitlist();
   }
   ```

2. 使用Relative Path

   ```rust
   pub fn eat_at_restaurant() {
       front_of_house::hosting::add_to_waitlist();
   }
   ```

   `eat_at_restaurant`和`front_of_house`都位于crate根下，所以可以这样使用相对位置定位`front_of_house`。

3. 使用`use`将一个模块引入当前scope

   ```rust
   use crate::front_of_house::hosting;
   pub fn eat_at_restaurant() {
       hosting::add_to_waitlist();
   }
   ```

   相当于为`hosting`模块建立了一个快捷方式，在`use`之后的作用域中，`hosting`模块可以直接使用。

4. 直接使用`use`将`add_to_waitlist`引入当前scope

   ```rust
   use crate::front_of_house::hosting::add_to_waitlist;
   pub fn eat_at_restaurant() {
       add_to_waitlist();
   }
   ```

   > 一般不直接引入函数，这样可以和本地函数做区分
   >
   > 一般直接引入struct、enum和其他项

使用相对路径时，如果要访问父模块中的代码时，可以使用`super`来定位父模块中的内容（相当于文件系统中的`../`）：

```rust
fn deliver_order() {}

mod back_of_house {
    fn cook_order() {}
    fn fix_incorrect_order() {
        cook_order();
        super::deliver_order();
    }
}
```

在上述的例子中，`fix_incorrect_order`函数可以...

- 直接调用同级的`cook_order`
- 通过`super`来定位父模块中的`deliver_order`

## Module中的Struct和Enum

- 一个struct中的field默认private，需要使用`pub`为单独的field允许外部访问权限
- 一个enum中的项默认public

### Struct

```rust
mod back_of_house {
    pub struct Breakfast {
        pub toast: String,
        seasonal_fruit: String,
    }
}
```

由于`Breakfast`中有私有field`seasonal_fruit`，所以需要一个**公有的**构造函数来在外部创建这个struct：

```rust
mod back_of_house {
    pub struct Breakfast {
        pub toast: String,
        seasonal_fruit: String,
    }

    impl Breakfast {
        pub fn summer(toast: &str) -> Breakfast {
            Breakfast {
                toast: String::from(toast),
                seasonal_fruit: String::from("peaches"),
            }
        }
    }
}
```

使用`Breakfast`：

```rust
pub fn eat_at_restaurant() {
    let mut meal = back_of_house::Breakfast::summer("Rye");  // 使用构造函数创建`Breakfast`的实例
    meal.toast = String::from("Wheat");  // 更改public field
    println!("I'd like {} toast please", meal.toast);  // 调用public field
	// 但是不允许从外部直接修改private field
    // meal.seasonal_fruit = String::from("blueberries");
}
```

### Enum

```rust
mod back_of_house {
    pub enum Appetizer {
        Soup,
        Salad,
    }
}

pub fn eat_at_restaurant() {
    let order1 = back_of_house::Appetizer::Soup;
    let order2 = back_of_house::Appetizer::Salad;
}
```

没有什么需要特殊说明的。

## 更多`use`的用法

引入同一个模块下的多个子模块：

```rust
use std::fmt;
use std::io;
// 可以被简写为：
use std::{fmt, io};
```

引入一个模块和它之下的子模块：

```rust
use std::io;
use std::io::Write;
// 可以被简写为：
use std::io::{self, Write};
```

引入一个模块下的所有子模块（慎用）：

```rust
use std::collections::*;
```

重命名引入（避免命名冲突）：

```rust
use std::fmt::Result;
use std::io::Result as IoResult;
```

Re-exporting:

```rust
pub use crate::front_of_house::hosting;
```

> 在re-exporting之前，外部代码如果要使用的话需要使用`restaurant::front_of_house::hosting::add_to_waitlist()`来调用这个函数；之后使用`restaurant::hosting::add_to_waitlist()`即可。

## 将一个Module拆成多个文件

原本的文件结构：

```
.
├── Cargo.lock
├── Cargo.toml
└── src
    └── lib.rs
```

```rust
// src/lib.rs
mod front_of_house {  // use keyword `mod` to define a module
    pub mod hosting {
        pub fn add_to_waitlist() {}
        fn seat_at_table() {}
    }
    mod serving {
        fn take_order() {}                                                 
        fn serve_order() {}
        fn take_payment() {}
    }
}
```

然后将`front_of_house`移动到单独的文件中：

```
.
├── Cargo.lock
├── Cargo.toml
└── src
    ├── front_of_house.rs
    └── lib.rs
```

```rust
// src/lib.rs
mod front_of_house;  // 只保留声明
```

```rust
// src/front_of_house.rs
pub mod hosting {
    pub fn add_to_waitlist() {}
    fn seat_at_table() {}
}
mod serving {
    fn take_order() {}                                                 
    fn serve_order() {}
    fn take_payment() {}
}
```

同理，`src/front_of_house.rs`中的`hosting`模块也可以被移动到单独的文件中：

```
.
├── Cargo.lock
├── Cargo.toml
└── src
    ├── front_of_house
    │   └── hosting.rs
    ├── front_of_house.rs
    └── lib.rs
```

```rust
// src/lib.rs
mod front_of_house;  // 只保留声明
```

```rust
// src/front_of_house.rs
pub mod hosting;  // 同样只保留声明
mod serving {
    fn take_order() {}                                                 
    fn serve_order() {}
    fn take_payment() {}
}
```

```rust
// src/front_of_house/hosting.rs
pub fn add_to_waitlist() {}
fn seat_at_table() {}
```

