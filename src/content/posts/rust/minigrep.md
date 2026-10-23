---
title: Rust Book中的命令行程序：minigrep实现记录
published: 2026-10-21
description: ''
image: ''
tags: ["rust", "笔记", "计算机"]
category: 'Rust'
draft: false
lang: 'zh_CN'
---
使用cargo创建一个二进制carte项目：

```shell
$ cargo new minigrep
$ cd minigrep
```

核心行为：

- 获取一个**文件路径**和一个**字符串**作为参数
- 找到在这个文件中包含字符串的行
- 输出

这需要：

- 读取命令行参数
- 读取文件
- 读取环境变量
- 将错误信息打印到**标准错误流** `stderr`

在测试阶段，使用艾米莉·狄金森（Emily Dickinson）的诗作为例子：

```
I'm nobody! Who are you?
Are you nobody, too?
Then there's a pair of us - don't tell!
They'd banish us, you know.

How dreary to be somebody!
How public, like a frog
To tell your name the livelong day
To an admiring bog!
```

将这个诗储存为`poem.txt`

## 接受命令行参数

使用`std::env`模块中的`args()`获取参数：

```rust
// src/main.rs
use std::env;
fn main() {
    let args: Vec<String> = env::args().collect();
    dbg!(&args);
}
```

- `args()`会返回一个迭代器*iterator*
- `collect()`将迭代器转换成一个集合
- 使用`collect()`给`Vec`类型赋值时，需要显式地标注出类型`Vec<String>`，因为编译器无法推断`collect()`返回的值

使用cargo运行这个项目时，使用`--`选项将自定义的参数传递给`main.rs`：

```shell
$ cargo run -- hello world
```

输出结果：

```
[src/main.rs:4:5] args = [
    "target/debug/minigrep",
    "hello",
    "world",
]
```

从这个输出结果可以看到，`args`的第一个参数（索引`0`）是这个程序本身，后面从索引`1`开始才是第一个用户传入的参数。

### 将参数存入变量

通过`args`储存参数：

```rust
	// --snip--
	let query = &args[1];
    let file_path = &args[2];

    println!("Looking for '{query}' in the file '{file_path}'");
```

输出：

```shell
$ cargo run -- hello world
Looking for 'hello' in the file 'world'
```

## 读取文件

引入`std::fs`模块，使用`read_to_string()`将一个文本文件中的内容储存到一个String中：

```rust
use std::env;
use std::fs;
fn main() {
    let args: Vec<String> = env::args().collect();
    let query = &args[1];
    let file_path = &args[2];

    println!("Looking for '{query}' in the file '{file_path}'");
    
    let contents = fs::read_to_string(file_path)
        .expect("Should havee been able to read the file");
        
    println!("The contents in the file '{file_path}:\n{contents}'");
}
```

运行：`cargo run -- you poem.txt

输出结果：

```
Looking for 'you' in the file 'poem.txt'
The contents in the file 'poem.txt':
I'm nobody! Who are you?
Are you nobody, too?
Then there's a pair of us - don't tell!
They'd banish us, you know.

How dreary to be somebody!
How public, like a frog
To tell your name the livelong day
To an admiring bog!
```

*符合预期*

但是如果文件不存在（读取其他文件中的内容时，会发生错误）：

```
Looking for 'you' in the file 'poem2.txt'

thread 'main' (169875) panicked at src/main.rs:13:10:
Should havee been able to read the file: Os { code: 2, kind: NotFound, message: "No such file or direc
tory" }
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

## 重新审查并改进项目

目前的问题：

- 所有的逻辑都在`main`函数中，功能不分离的话在日后的开发中，`main`函数会越来越臃肿，难以测试
- `query`和`file_path`变量负责管理用户的参数，而`contents`储存文件中的信息，当变量变多时，会难以追踪每个变量的目的
- 所有问题都会共用同一个错误信息
- 如果用户提供的参数不够时，rust会产生`index out of bounds`的错误，这个错误并不能告诉用户擦生了什么

一般来说，rustaceans会将一个项目分成多个文件：

- `main.rs` - 负责处理程序运行
- `lib.rs`等其他模块文件 - 负责处理真正的任务逻辑

### 分离`main`函数中的逻辑 1

> 分离“解析命令行”（配置）的逻辑

首先将处理命令行解析的功能专门提取到一个专门的函数中：

```rust
fn parse_config(args: &[String]) -> (&str, &str) {
    (&args[1], &args[2])
}
```

这个函数返回一个tuple，外部的函数可以接收这这个tuple：

```rust
	let (query, file_path) = parse_config(&args);
```

但是这样返回的数据别人没办法第一时间知道是什么，将结果封装到一个struct中是最好的：

```rust
struct Config {
    query: String,
    file_path: String,
}
```

既然创建了一个专门的struct，正好可以为这个struct添加一个构造函数，可以通过`args`创建类型：

```rust
impl Config {
    fn new(args: &[String]) -> Config {
        let query = args[1].to_string();
        let file_path = args[2].to_string();
        Config { query, file_path }
    }
}
```

同时更改main函数以适配这次更改：

```rust
	// --snip--
    let config = Config::new(&args);

    println!("Looking for '{}' in the file '{}'", config.query, config.file_path);
    
    let contents = fs::read_to_string(config.file_path.clone())
        .expect("Should havee been able to read the file");
    println!("The contents in the file '{}':\n{contents}", config.file_path);
    // --snip--
```

注意这个地方使用了`clone()`方法：`fs::read_to_string(config.file_path.clone())`，为了确保`config.file_path()`之后还可以使用。更好的方法应该是写一个`getter`方法来获取`file_path`的值。

### 完善错误处理

如果传递的参数小于两个，也就是说`env::args()`少于3个元素。

这个错误最开始会发生在`Config::new()`中，这个函数的崩溃会直接导致整个`main`函数的崩溃。为此，可以通过更改`Config::new()`到`Config::build()`，让`build`返回一个Result类型，这样就可以handle the error了：

```rust
    fn build(args: &[String]) -> Result<Config, &'static str> {
        if args.len() < 3 {
            return Err("Not enough arguments!")
        }

        let query = args[1].clone();
        let file_path = args[2].clone();
        Ok(Config { query, file_path })
    }
```

然后在`main`中处理可能出错的情况：

```rust
    let config = Config::build(&args).unwrap_or_else(|err| {
        println!("Problem parsing arguments: {err}");
        process::exit(1);
    });
```

这样一来，发生错误时的报错信息就更加易读了：

```
Problem parsing arguments: Not enough arguments!
```

### 分离main函数中的逻辑 2

> 分离“读取/执行”的逻辑

将读取文件相关的代码移到单独的`run`函数中：

```rust
fn run(config: Config) -> Result<(), Box<dyn Error>> {
    let contents = fs::read_to_string(config.file_path.clone())?;
    println!("The contents in the file '{}':\n{contents}", config.file_path);
    Ok(())
}
```

这个函数返回Result类型来处理错误，并使用`?`运算符来在遇到问题时提前返回。

因此这个函数返回值的类型是`Result<(), Box<dyn Error>>`，使用`?`时的错误类型一般是`Box<dyn Error>`

同时，在`main`中也需要添加相应的错误处理逻辑：

```rust
    if let Err(e) = run(config) {
        println!("Application error: {e}");
        process::exit(1);
    }
```

这里用到的两种错误处理方式：

1. `unwrap_or_else()` - 使用这个方法时，可以保留没有发生错误时的道德结果，并赋值到一个变量上。
2. `if let Err(e) = ...` - 用于匹配发生错误的情况，使用`if let`时一般不关心调用的函数的返回值，只在乎有没有发生错误。

### 拆分代码到`lib.rs`

> 将“搜索逻辑”放在独立的`lib.rs`中

新建`src/lib.rs`文件，添加（还没有实现的）搜索逻辑：

```rust
// src/lib.rs
pub fn search<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    unimplemented!();
}
```

在`src/main.rs`中引入并调用这段还未实现的函数：

```rust
// src/main.rs

// --snip--
use minigrep::search;

// --snip--
fn run(config: Config) -> Result<(), Box<dyn Error>> {
    // --snip--
    for line in search(&config.query, &contents) {
        println!("{line}");
    }
    Ok(())
}
```

## 遵循测试驱动开发（TDD）

测试驱动开发，Test Driven Development，简单来说就是先写“单元测试”的代码，然后根据测期望的结果，来实现函数的行为，例如`lib.rs`中的`search`还没有实现任何行为，但可以先写测试代码。

### 先写测试代码

```rust
// src/lib.rs
pub fn search<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    unimplemented!();
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn one_result() {
        let query = "duct";
        let contents = "\
Rust:
safe, fast, productive.
Pick three.";
        assert_eq!(vec!["safe, fast, productive."], search(query, contents));
    }
}
```

如果这时对这个程序测试，会得到`not implemented`的错误：

```shell
$ cargo test

running 1 test
test tests::one_result ... FAILED

failures:

---- tests::one_result stdout ----

thread 'tests::one_result' (6498) panicked at src/lib.rs:2:5:
not implemented
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace


failures:
    tests::one_result

test result: FAILED. 0 passed; 1 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
```

### 实现逻辑

具体过程可以参考Rust Book，Rust Book中写得很详细：

```rust
pub fn search<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    let mut result = Vec::new();
    for line in contents.lines() {
        if line.contains(query) {
            result.push(line);
        }
    }
    result
}
```

其中关键点：

- `contents.lines()` - rust中通过一行一行遍历字符串的方法叫`lines()`，类似于python中的`.split("\n")`
- `line.contains(query)` - 判断字符串中是否包含某个内容，类似于python中的`query in line`

实现逻辑后再次运行测试`cargo test`：

```shell
$ cargo test

running 1 test
test tests::one_result ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 
```

现在运行这个程序应该也可以得到期望的结果：

```rust
$ cargo run -- you poem.txt
I'm nobody! Who are you?
Are you nobody, too?
They'd banish us, you know.
To tell your name the livelong day
```

> 之前为了测试，有一些`println!()`的代码，现在这些代码已经可以移除了

实际使用的例子：

```shell
$ cargo build --release
$ ./target/release/minigrep let src/main.rs
    let args: Vec<String> = env::args().collect();
    let config = Config::build(&args).unwrap_or_else(|err| {
    if let Err(e) = run(config) {
    let contents = fs::read_to_string(config.file_path.clone())?;
        let query = args[1].clone();
        let file_path = args[2].clone();
```

### 新增功能

> feat: 增加用户可以通过设置**环境变量**来选择是否启用大小写敏感

因为这部分和之前相比算是新的功能，所以之前的测试代码用不了了。

遵循TDD，还是先写测试代码：

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn case_sensitive() {
        let query = "duct";
        let contents = "\
Rust:
safe, fast, productive.
Pick three.
Duct tape.";

        assert_eq!(vec!["safe, fast, productive."], search(query, contents));
    }

    #[test]
    fn case_insensitive() {
        let query = "rUsT";
        let contents = "\
Rust:
safe, fast, productive.
Pick three.
Trust me.";

        assert_eq!(
            vec!["Rust:", "Trust me."],
            search_case_insensitive(query, contents)
        );
    }
}
```

同时新增`search_case_insensitive`：

```rust
pub fn search_case_insensitive<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    let query = query.to_lowercase();
    let mut result = Vec::new();
    for line in contents.lines() {
        if line.to_lowercase().contains(&query) {
            result.push(line);
        }
    }
    result
}
```

这个代码可以不区分大小写的原理在于，对要搜索的词和原文都取小写/大写，导致根本没有大小写区别。

- `to_lowercase`实在创建新数据，所以需要分配一个新的String。

跑测试检测成果：

```
running 2 tests
test tests::case_insensitive ... ok
test tests::case_sensitive ... ok

test result: ok. 2 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
```

然后更改`Config`的定义，让它可以接受用于切换大小写敏感的选项：

```rust
struct Config {
    query: String,
    file_path: String,
    ignore_case: bool,
}
```

然后更改`run`，让它可以区分不同情况：

```rust
fn run(config: Config) -> Result<(), Box<dyn Error>> {
    let contents = fs::read_to_string(config.file_path.clone())?;
    // println!("The contents in the file '{}':\n{contents}", config.file_path);
    let result = match config.ignore_case {
        true => search_case_insensitive(&config.query, &contents),
        false => search(&config.query, &contents),
    };
    for line in result {
        println!("{line}");
    }
    Ok(())
}
```

鉴于用户是设置**环境变量**，所以可以通过`env::var("VARIABLE_NAME")`来读取环境变量：

```rust
		// --snip--
        let query = args[1].clone();
        let file_path = args[2].clone();
        let ignore_case = env::var("IGNORE_CASE").is_ok();
        Ok(Config { query, file_path, ignore_case })
```

- `is_ok()` - 检测IGNORE_CASE有没有被设置，如果被设置了（任意值都可以），那么返回`true`，进行忽略大小写搜索

---

展示：

```shell
$ IGNORE_CASE=1 cr -- are poem.txt
I'm nobody! Who are you?
Are you nobody, too?

$ cr -- are poem.txt 
I'm nobody! Who are you?
```

## 将错误重定向到`stderr`

- `println!()` - 打印信息到`stdout`
- `epringln!()` - 打印信息到`stderr`

```rust
    let config = Config::build(&args).unwrap_or_else(|err| {
        eprintln!("Problem parsing arguments: {err}");
        process::exit(1);
    });

    if let Err(e) = run(config) {
        eprintln!("Application error: {e}");
        process::exit(1);
    }
```