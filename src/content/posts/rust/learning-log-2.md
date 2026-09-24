---
title: Rust学习笔记 2 - String, Reference and Slice
published: 2026-09-22
description: ''
image: ''
tags: ["rust", "笔记", "计算机"]
category: 'Rust'
draft: false
lang: 'zh_CN'
---

- [The Basis of Using `String` Type in Rust](#the-basis-of-using-string-type-in-rust)
- [Ownership and String](#ownership-and-string)
- [String and Slice](#string-and-slice)

**背景问题**：

通常来说，常规的变量类型在内存中占用的空间位置是固定的，可以存储在stack上，包括int和float、char和str（字符串的字面量literal）、甚至固定长度的array；但是String（rust中实现的字符串类型）的长度未知，只能用heap实现。

## The Basis of Using `String` Type in Rust

通过string literal来创建`String`：

```rust
let s = String::from("hello");
```

创建一个可以被拼接的`String`：

```rust
let mut s = String::from("hello");
s.push_str(", world!");  // add another string behind 
println!("{s}");
```

完全更改一个`String`的值：

```rust
let mut s = String::from("hello");
s = String::from("ahoy");
```

当一个`String`的初始值被一个完全新的内容替换掉时，初始值所占用的内存会被释放，而`String`会指向新内容所在内存地址。

`String`不能通过直接赋值的方式赋值给别的变量，而是要通过`String`中专门的`clone()`方法：

```rust
let s1 = String::from("hello");
let s2 = s1.clone();
println!("s1 = {s1}, s2 = {s2}");
```

“直接赋值”所做的事情是为这个`String`更换标志符：

```rust
let s1 = String::from("hello");
let s2 = s1;
```

这个操作相当于把`s1`中的东西移动到了`s2`里，而`s1`不再可以被访问。

> 但是其他有`Copy` trait的类型可以在赋值给别的变量后仍然有效，例如int：
>
> ```rust
> let x = 5;                                           
> let y = x;  // the value of x is copied to y         
> println!("x = {x}, y = {y}");  // this copy is valid 
> ```

## Ownership and String

由于`let s2 = s1;`会把`s1`中的东西移动到`s2`中，而`s1`不再有效，这一点对给函数中传参数也适用：

```rust
fn main() {
	let s = String::from("hello");
	takes_ownership(s);  // the ownership of s is transferred into the function
	// println!("{s}");  // s is no longer valid here, error
}
fn takes_ownership(some_string: String) {  // some_string enters the scope
    println!("{some_string}");
}
// some_string leaves the scope, drop() method is being called to release the memory
```

当`main()`中的`s`进入`takes_ownership()`时，`s`中的内容就被移动到了`some_string`中，而原本的`s`被丢弃了。

这个问题可以被一种丑陋的方式解决：返回值

```rust
fn main() {
    let s1 = gives_ownership();
	let s2 = String::from("hello");   
	let s3 = takes_and_gives_bake(s2);
}
fn gives_ownership() -> String { String::from("yours") }
fn takes_and_gives_bake(a_string: String) -> String {
    println!("{a_string}");
    return a_string
}
```

这种方式过于难以维护了，参考：

```rust
fn main() {
    // calculate the length from string
    let s1 = String::from("some random text");
    let (s1, len) = calculate_length(s1);
    println!("The string \"{s1}\" has length of {len}");
}
fn calculate_length(s: String) -> (String, usize) {
    let length = s.len();
    return (s, length)  // return the original string content with the calculated length
}
```

---

为了改进这一点，可以使用`String`的reference而不是使用`String`中本身的数据：

使用reference重新实现上面的`calculate_length()`：

```rust
fn main() {
    let s1 = String::from("hello");
    let len = calculate_length(&s1);  // `&s1` means the location of the variable `s1`
    println!("The length of \"{s1}\" is {len}.");
}
fn calculate_length(s: &String) -> usize {  // `&String` means the location to a String
    return s.len()
}
```

这时传入`calculate_length()`的参数是`s1`所在的内存地址，在`calculate_length()`中，`s`会使用`s1`的内存地址，这样所有权问题就不会产生了。

可变变量可以有可变引用：

```rust
fn main() {
    let mut s = String::from("hello");  // firstly, `s` has to be immutable
    change(&mut s);                     // pass immutable pointer of `s` into the function
    println!("s = {s}");
}
fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

默认情况下引用是不可变的，正如变量默认是不可变的。

## String and Slice

下面这段代码会找到一个string中的第一个空格`' '`的位置：

```rust
fn main() {
    let s = String::from("hello world");
    let word = first_word(&s);
    println!("The first space of \"{s}\" is at {word}");
}

fn first_word(s: &String) -> usize {
    let bytes = s.as_bytes();
    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return i  // return the first location of `space`
        }
    };
    s.len()  // if no `space` is found, then return the length of the string
}
```

- `.as_bytes()` converts the string to an array of bytes
- `bytes.iter()` creates an iterator on the array
- `.enumerate()` return the each element with its index

但是用独立的数字来维护单个word实在太丑陋了，用slice可以储存一段`str`。

```rust
let s = String::from("hello world");
let hello = &s[0..5];
let world = &s[6..11];
println!("&s[0..5] = {hello}, &s[6..11] = {world}");
```

- `&s[0..2] = &s[..2]`
- `&s[3..len] = &s[3..]`
- `&s[0..len] = &s[..]`

用slice的方式实现`first_word()`就会很优雅：

```rust
fn first_word_slice(s: &str) -> &str {
    let bytes = s.as_bytes();
    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[..i]
        }
    }
    return &s[..]
}
```

参数类型：`&str`，`&str`类型可以同时对`&String`值和`&str`值生效

返回值：Slice的数据类型是`&str`

除了字符串，slice也可以应用到其他的类型上：

```rust
let a = [1, 2, 3, 4, 5];
let slice = &a[1..3];
assert_eq!(slice, &[2, 3]);
```

