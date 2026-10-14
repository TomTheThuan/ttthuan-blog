---
title: Rust学习笔记 7 - Error Handling
published: 2026-10-14
description: ''
image: ''
tags: ["rust", "笔记", "计算机"]
category: 'Rust'
draft: false
lang: 'zh_CN'
---

Rust中的错误可以分成两大类：

- recoverable - 例如“文件未找到”之类的，对程序运行上无关紧要的问题，或者说可以在运行时处理的问题
- unrecoverable - 基本上是程序的bug

对于recoverable的错误，在rust中通常使用`Result<T, E>`类型的函数返回值来处理。

而使用`panic!`宏则会在发生错误时立即退出。

**使用`panic!`**：

```rust
// src/main.rs
fn main() {
    panic!("crash and burn");
}
```

主动触发错误后输出：

```shell
$ cargo run
   Compiling panic v0.1.0 (file:///projects/panic)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.25s
     Running `target/debug/panic`

thread 'main' panicked at src/main.rs:2:5:
crash and burn
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

## Raise an Error

将一个函数的返回值设为`Result<T, E>`，并将`Ok()`或`Err()`作为返回值：

```rust
fn generate_nametag_text(name: String) -> Result<String, String> {
    //                                    ^^^^^^         ^^^^^^
    if name.is_empty() {
        Err("Empty names aren't allowed".to_string())
    } else {
        Ok(format!("Hi! My name is {name}"))
    }
}
```

在函数中调用函数时，内部的函数返回`Err()`，可以用`?`将错误propagate出去（详见下）

```rust
    let qty = item_quantity.parse::<i32>()?;
    //                                    ^ added
```
## Manage the recoverable error by `match`

使用`Result<T, E>`处理**recoverable**错误：

rust中内置了一个enum类型 - `Result<T, E>`：
```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

使用match语句处理`Result<T, E>`：

```rust
use std::fs::File;
fn main() {
    let username_file_result = File::open("name.txt");
    let username_file = match greeting_file_result {
        Ok(file) => file,
        Err(error) => panic!("Problem opening the file: {error:?}"),
    };
}
```

1. 获取文件，将获取后的文件存在`username_file_result`中
2. 使用match匹配result
   - `Ok`时返回获取到的文件
   - `Err`时`panic!`并输出错误信息

还可以调用一个错误的`kind()`方法来得知发生的是那种错误：

```rust
        Err(error) => match error.kind() {
            ErrorKind::NotFound => match File::create("hello.txt") {
                Ok(fc) => fc,
                Err(e) => panic!("Problem creating the file: {e:?}"),
            },
            _ => {
                panic!("Problem opening the file: {error:?}");
            }
        },
```

1. 使用`kind()`获取错误类型
2. 如果文件不存在（`error.kind() == ErrorKind::NotFound`）则创建新文件
3. 如果操作不成功（不能创建文件或不是`NotFound`）的问题

## Closure and `unwarp`

使用**闭包**和`unwarp_or_else()`实现相同的逻辑：

关于“闭包”，闭包可以理解为一个可以获得环境中变量的“匿名函数”。

```rust
fn add_one(x: i32) -> i32 { x + 1 }  // 普通函数
let add_one = |x: i32| -> i32 { x + 1 };  // 闭包
let add_one = |x| x + 1;  // 类型可省略，编译器能推断
```

获取环境中的变量：

```rust
let base = 10;
let add = |x| x + base;   // 捕获了外部的 base
println!("{}", add(5));   // 15
```

关于`unwarp_or_else()`，这个函数对`Option`类型和`Result`类型生效，它：

- 如果结果是`Some()`或`Ok()`，则直接返回里面的结果
- 如果结果是`None`或`Err()`，则**调用作为参数传入的函数**（可以是闭包的匿名函数）来处理结果

```rust
    let greeting_file = File::open("hello.txt").unwrap_or_else(|error| {
        if error.kind() == ErrorKind::NotFound {
            File::create("hello.txt").unwrap_or_else(|error| {
                panic!("Problem creating the file: {error:?}");
            })
        } else {
            panic!("Problem opening the file: {error:?}");
        }
    });
```

上面发生错误时，`unwarp_or_else`会调用闭包（匿名函数），将`Err(e)`中的错误`e`作为参数传入闭包，在闭包中的错误详情则是`error`变量（闭包中定义的parameter）。

---

还有其他`unwarp`家族的函数：

| 方法                | 参数       | 求值时机                   |
| :------------------ | :--------- | :------------------------- |
| `unwarp()`          | 无         | 自动调用`panic!`，退出程序 |
| `unwrap_or(v)`      | 一个值 `v` | **总是**先算好 `v`         |
| `unwrap_or_else(f)` | 一个闭包   | 只在 `None`/`Err` 时才调用 |

`unwarp_or()`可以直接给一个**默认值**

```rust
let opt: Option<i32> = None;
opt.unwrap_or(0)  // value: 0
```

`unwarp()`函数还有一个“近亲”，`expect()`，使用`expect()`时可以自定义错误信息：

```rust
let greeting_file = File::open("hello.txt")
        .expect("hello.txt should be included in this project");
```

## Propagating Error

当一个返回值为`Result<T, E>`类型的函数中调用别的函数时，别的函数返回了`Err()`错误的问题时，大多数时候希望将里面的`Err()`作为返回值提供给外部的函数。这时需要“传播”错误。

使用示例：

```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
    let username_file_result = File::open("hello.txt");
    let mut username_file = match username_file_result {
        Ok(file) => file,
        Err(e) => return Err(e),
    };

    let mut username = String::new();
    match username_file.read_to_string(&mut username) {
        Ok(_) => Ok(username),
        Err(e) => Err(e),
    }
}
```

在上面的实例中，如果match里匹配到了`Err()`，在直接返回这个错误本身：`Err(e) => return Err(e)`

如果一直这样写的话太繁琐了，所以rust中有个专门的语法糖`?`：如果当前函数的返回值是`Result<T, E>`类型，那么这个函数中可以直接对`Result<T, E>`类型使用`?`，直接propagate错误。

```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
    let mut username_file = File::open("name.txt")?;
    
    let mut username = String::new();
    username_file.read_to_string(&mut username)?;
    
    username = username.trim().to_string();
    Ok(username)
}

```

这样写下来就相比上面使用`match`语句的方法简洁很多。

还可以通过chain call来使用`?`：

```rust
fn last_char_of_first_line(text: &str) -> Option<char> {
    text.lines().next()?.chars().last()
}
```

---

通过更改`main`函数的返回值，可以让`main`函数也使用`?`：

- 函数签名：`fn main() -> Result<(), Box<dyn Error>>;`
- 最后在函数结尾添加`Ok(())`来使`main`函数的返回值和签名匹配

```rust
use std::error::Error;
use std::fs::File;
fn main() -> Result<(), Box<dyn Error>> {
    let greeting_file = File::open("hello.txt")?;
    Ok(())
}
```

