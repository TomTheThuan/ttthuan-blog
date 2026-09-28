---
title: Rust学习笔记 4 - match
published: 2026-09-24
description: ''
image: ''
tags: ["rust", "笔记", "计算机"]
category: 'Rust'
draft: false
lang: 'zh_CN'
---

- [`match`](#match)
  - [常规使用](#常规使用)
  - [用于匹配`Option<T>`](#用于匹配optiont)
  - [通配模式和占位符`_`](#通配模式和占位符_)
- [Concise Control Flow](#concise-control-flow)
  - [`if-let`](#if-let)
  - [Use `let ... else` to Keep on "Happy Path"](#use-let--else-to-keep-on-happy-path)

## `match`

关于`match`，之前也有过使用match的地方：[Enum with Implemented Function](https://thyrius.top/posts/rust/learning-log-3/#enum-with-implemented-function)

### 常规使用

`match`一般用于匹配一系列枚举值（enum values）：

```rust
#[derive(Debug)]
enum UsState {
    Alabama,
    Alaska,
}
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(UsState),
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => {
            println!("Lucky penny!");
            1
        },
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter(state) => {
            println!("State quarter from {state:?}!");
            25
        },
    }
}
```

每个match分支的代码都是一个表达式，当一个分支被选中时，这个表达式的结果将作为整个`match`表达式的结果。

- 如果是段表达式，则不需要使用大括号，只需在表达式末尾添加`,`即可
- 如果是比较长的表达式，可以使用大括号将整段表达式包起来，这时末尾的`,`是可选的（个人认为最好还是加上比较好）

上面有“打印出枚举值”的操作，原理是：

1. 为目标enum type添加`#[derive(Debug)]`标签

2. 在`println!()`中调用`{enum:?}`使用，参考：

   ```rust
   let state = UsState::Alabama;
   println!("{state:?}");
   ```

### 用于匹配`Option<T>`

使用match来匹配`Option<T>`，并执行相关的计算：

- 一个数字可能是`Some(number)`或者`None`
- 当这个数字是`Some(number)`时，返回`Some(number + 1)`，否则返回`None`

```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        Some(i) => Some(i + 1),
        None => None,
    }
}

let five = Some(5);
dbg!(plus_one(five));
let none = None;
dbg!(plus_one(none));
```

输出结果：

```
[src/main.rs:93:5] plus_one(five) = Some(6)
[src/main.rs:95:5] plus_one(none) = None
```

> [!IMPORTANT]
>
> **匹配是穷尽的 [Matches Are Exhaustive](https://doc.rust-lang.org/book/ch06-02-match.html#matches-are-exhaustive)**
>
> `match`语句必须匹配到所有情况，不让会有报错，以下这个操作就是不被允许的（没有匹配`None`的情况）：
>
> ```rust
> fn plus_one(x: Option<i32>) -> Option<i32> {
>     match x {
>         Some(i) => Some(i + 1),
>     }
> }
> 
> ```

### 通配模式和占位符`_`

> Reference [Catch-All Patterns and the `_` Placeholder](https://doc.rust-lang.org/book/ch06-02-match.html#catch-all-patterns-and-the-_-placeholder)

```rust
let dice_roll = 9;
match dice_roll {
    3 => println!("Three!"),
    7 => println!("Seven!"),
    other => println!("The number is {other}"),
};
```

上面的代码中，除了匹配`3`和`7`的情况，剩下的数字将被赋值到`other`这个变量上，同时进入该分支。

如果这个变量不会被使用时，可以使用占位符`_`:

```rust
let dice_roll = 9;
match dice_roll {
    3 => println!("Three!"),
    7 => println!("Seven!"),
    _ => (),
};
```

当数字不是`3`或`7`时，什么事情都不做。

我个人觉得占位符`_`主要做的事情就是忽略掉一些不必要的赋值操作。例如要重复执行某几次操作，但不需要知道当前是第几次操作，就可以这样写：

```rust
for _ in (1..4) {
    println!("Meow");
}

// Output：
// Meow
// Meow
// Meow
```

> [!IMPORTANT]
>
> 值得注意的是，通配模式必须要放在`match`的最后一个分支，不然rust按顺序进行匹配时，通配模式之后的分支永远不会被匹配到。

## Concise Control Flow

`if-let`和`let-else`语句按我的理解来说，算是比较好用的一种语法糖，可以很简单地做到一些常用但冗长的操作。

### `if-let`

```rust
    let config_max = Some(3u8);
    match config_max {
        Some(max) => println!("The maximun is configured to be {max}"),
        _ => (),
    };
```

上面`match`语句的`_ -> ()`在`if-let`语法中可以被忽略掉：

```rust
    let config_max = Some(3u8);
    if let Some(max) = config_max {
        println!("The maximum is configured to be {max}");
    };
```

在只关心特定情况时的`match`语句时，可以用`if-let`来代替。

按Rust Book所说：“`if let` 语法获取通过等号分隔的一个模式和一个表达式。”

> 体感上来说确实是这样的，这像是尝试对某个变量赋值，赋值成功时执行`if`中的内容，失败时则跳过，这种感觉。下属代码跑得通：
>
> ```rust
>     if let x = 3 {
>         println!("Assign x: {x}");
>     } else {
>         println!("Failed to assign x");
>     };
> ```
>
> 但是：
>
> ```
> warning: irrefutable `if let` pattern
>    --> src/main.rs:124:8
>     |
> 124 |     if let x = 3 {
>     |        ^^^^^^^^^
>     |
>     = note: this pattern will always match, so the `if let` is useless
>     = help: consider replacing the `if let` with a `let`
>     = note: `#[warn(irrefutable_let_patterns)]` on by default
> ```

常规来说，`if-let`语句可以这样使用：

```rust
    let coins = [
        Coin::Quarter(UsState::Alaska),
        Coin::Penny,
        Coin::Dime,
    ];
    let mut count = 0;
    println!("Initial Counter: {count}");
    for c in coins {
        if let Coin::Quarter(state) = c {
            println!("State quarter from {state:?}!");
        } else {
            println!("Current coin is not a quarter, increase the counter.");
            count += 1;
        }
    }
    println!("Final Counter: {count}");
```

输出：

```
Initial Counter: 0
State quarter from Alaska!
Current coin is not a quarter, increase the counter.
Current coin is not a quarter, increase the counter.
Final Counter: 2
```

### Use `let ... else` to Keep on "Happy Path"

> Reference [Staying on the “Happy Path” with `let...else`](https://doc.rust-lang.org/book/ch06-03-if-let.html#staying-on-the-happy-path-with-letelse)

`let-else`语句的作用和`if-let`语句的作用正好相反：

- `if-let`关心的是当赋值**成功**后，要做的事情
- `let-else`关心的是当赋值**失败**后，要做的事情

以上面的`Coin`和`UsState`为例：

如果使用`if-let`语法时：

```rust
fn describe_state_quarter_v1(coin: Coin) -> Option<String> {
    if let Coin::Quarter(state) = coin {
        if state.existed_in(1900) {
            Some(format!("{state:?} is pretty old, for America!"))
        } else {
            Some(format!("{state:?} is relatively new."))
        }
    } else {
        None
    }
}
```

真正关心的代码放在了一个额外的`if`嵌套中，这可行但不简洁易懂。可以使用提前return的方式来提前退出判断逻辑：

```rust
fn describe_state_quarter_v2(coin: Coin) -> Option<String> {
    let state = if let Coin::Quarter(state) = coin {
        state
    } else {
        return None;  // return the value earlier
    };
    if state.existed_in(1900) {
        Some(format!("{state:?} is pretty old, for America!"))
    } else {
        Some(format!("{state:?} is relatively new."))
    }
}
```

这段代码中的early return同样很繁琐，`let state = if let Coin::Quarter(state) = coin {...}`并没有简洁到哪去。

不过用上`let-else`语法后就简单很多：

```rust
fn describe_state_quarter_v3(coin: Coin) -> Option<String> {
    let Coin::Quarter(state) = coin else {
        return None;
    };  // enter the else-flow when the assignment has failed
    if state.existed_in(1900) {
        Some(format!("{state:?} is pretty old, for America!"))
    } else {
        Some(format!("{state:?} is relatively new."))
    }
}
```

> 注意，这种写法能让函数主体沿着“愉快路径”（“Happy Path”）继续向下，而不必像 `if let` 那样让两个分支具有明显不同的控制流。

---

当使用`match`语法过于冗长时，可以尝试使用`if-let`或者`let-else`这两个语法糖。
