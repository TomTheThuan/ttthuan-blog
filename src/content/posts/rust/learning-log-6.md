---
title: Rust学习笔记 6 - Collections
published: 2026-10-05
description: ''
image: ''
tags: ["rust", "笔记", "计算机"]
category: 'Rust'
draft: false
lang: 'zh_CN'
---

Collections集合中有三个很长用的数据类型：

- `Vector` - 相当于可变长度的列表
- `String` - 可变长度的字符串（继承自`Vector`）
- `HashMap` - 相当于python中的`dict`字典

## `Vector`

创建一个`Vector`列表：

```rust
    let v: Vec<i32> = Vec::new();
    let v = vec![1, 2, 3];
```

添加元素：

```rust
    let mut v = Vec::new();
    for n in 1..6 {
        v.push(n);
    }
```

> 使用pop移除列表末尾的元素

读取元素：

1. 使用index

   ```rust
       let v = vec![1, 2, 3, 5];
       let third: &i32 = &v[2];
       println!("THe third element is {third}");
   ```

   直接使用这种方法可以访问，但是访问index外的元素时会使程序崩溃

2. 使用`get`方法

   ```rust
       let v = vec![1, 2, 3];
       let third: Option<&i32> = v.get(2);
       match third {
           Some(third) => println!("The third element is {third}"),
           None => println!("There is no third element."),
       }
   ```

   使用`get`方法获取时会返回一个`Option<T>`对象，可以用match来解析返回的结果

***！！！***当一个vec列表被更新时，原先来自这个vec列表的引用会失效，详细参见[Vectors - listing](https://doc.rust-lang.org/book/ch08-01-vectors.html#listing-8-6)

便利vec列表中的元素：

```rust
    let mut v = vec![100, 32, 57];
    for n in &mut v {
        *n += 50;
    }

    for n in &v {
        println!("{n}");
    }
```

> [!TIP]
>
> 一般来说vec中只允许存储同类型的元素，有个trick是通过建立一个enum类型的datatype，然后不同的enum允许储存不同类型的元素：
>
> ```rust
>     enum SpreadsheetCell {
>         Int(i32),
>         Float(f64),
>         Text(String),
>     }
>     let row = vec![
>         SpreadsheetCell::Int(3),
>         SpreadsheetCell::Float(10.12),
>         SpreadsheetCell::Text(String::from{"hello"})
>     ]
> ```

## `String`

创建`String`类型：

1. 使用构造函数建立：

   ```rust
       let mut s = String::new();
   
       let s = String::from("Hello, world!");
   ```

2. 使用`to_string`方法：

   ```rust
       let data = "hello, world!";
       let s = data.to_string();
   
       let s = "hello".to_string();
   ```

末尾合并字符串：

```rust
    let mut s = String::from("foo");
    s.push_str("bar");
```

> [!IMPORTANT]
>
> 当`push_str`的参数为`str`时，传入`push_str`的参数在使用后还可以再次被调用：
>
> ```rust
>     let mut s1 = String::from("foo");
>     let s2 = "bar";
>     s1.push_str(s2);
>     println!("s2 is {s2}");
> ```

> [!IMPORTANT]
>
> 使用`+`来连接字符串时，直接传入字符串本身会无法使用，但传入引用则没问题：
>
> ```rust
>     let s1 = String::from("Hello, ");
>     let s2 = String::from("world!");
>     let s3 = s1 + &s2;
>     // println!("s1: {s1}");  // s1 is moved, so it can't be accessed any more
>     println!("s2: {s2}");
>     println!("s3: {s3}");
> ```

格式化字符串（相当于python里的`f""`语法）：

```rust
    let s1 = String::from("tic");
    let s2 = String::from("tac");
    let s3 = String::from("toe");
    let s = format!("{s1}-{s2}-{s3}");
```

遍历一个字符串有多个含义：

- 遍历用于储存字符串的字节

  ```rust
      let s = "नमस्ते";
      for b in s.bytes() {
          println!("{b}");
      }
  ```

- unicode的标量值：[Unicode Scalar Value](https://unicodefyi.com/glossary/unicode-scalar-value/)

  ```rust
      let s = "नमस्ते";
      for c in s.chars() {
          println!("{c}");
      }
  ```

  ```
  न
  म
  स
  स्
  स्त
  स्ते
  ```

- unicode的字形簇：相当于“字母”的概念

## `HashMap`

> 所有key的数据类型要相同，所有value的数据类型要相同

创建一个`HashMap`类型：

```rust
    let mut scores = HashMap::new();
```

新增KV pair：

```rust
    scores.insert(String::from("Blue"), 10);
    scores.insert(String::from("Yellow"), 50);
    scores.entry(String::from("Blue")).or_insert(0);  // 仅在key不存在时新增
```

> [!TIP]
>
> 从`String`类型通过`insert`创建`HashMap`时，通过`clone`可以使`String`对象在创建`HashMap`后还可以再次使用：
>
> 创建后无法使用原本的`String`：
>
> ```rust
>     let new_team = String::from("Red");
>     let new_score = 30;
>     scores.insert(new_team, new_score);
> ```
>
> 还可以使用原本的`String`：
>
> ```rust
>     let new_team = String::from("Red");
>     let new_score = 30;
>     scores.insert(new_team.clone(), new_score);
>     
> 	println!("{new_team}: {new_score}");
> ```

更新KV pair，重新`insert`一遍即可：

```rust
    scores.insert(String::from("Blue"), 10);
    scores.insert(String::from("Blue"), 20);
```

打印一个`HashMap`，使用自带的Debug Trait即可：

```rust
    println!("{scores:?}");
```

用过key获取对应的value：

```rust
    let score = scores.get(&team_name).copied().unwrap_or(0);
```

其中：

1. `get`返回`Option<&T>`
2. `copied`返回`Option<T>`
3. `unwarp`将`Option<T>`中的`None`设为传入的参数（将参数作为key不存在时返回的默认值）

使用`HashMap`的reference遍历`HashMap`对象：

```rust
    for (key, val) in &scores {
        println!("{key}: {val}");
    }
```

### Example: Word Counter

> This Rust code counts how many times each word appears in a string.

```rust
    let text = "hello worlds wonderful world";
    let mut map = HashMap::new();
    for word in text.split_whitespace() {
        let count = map.entry(word).or_insert(0);
        *count += 1;
    }
    println!("{map:?}");
```

