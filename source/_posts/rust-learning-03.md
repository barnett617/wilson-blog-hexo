---
title: 通过类比其他编程语言的方式学习Rust语法-03
date: 2023-07-11 22:37:39
tags: [rust]
---

枚举和模式匹配

<!-- more -->

# 枚举定义

枚举和结构体类似，但它可以定义在多个值可能性下的某一种。

比如 IP 地址目前只有 ipv4 和 ipv6 两种，当定义一个 IP 地址时它只可能是这两种中的一种。

因此对于 IP 地址的类型范围就可以一个枚举，表示该类型所有的可能。

```rs
enum IpAddrKind {
    V4,
    V6,
}

let four = IpAddrKind::V4;
let six = IpAddrKind::V6;
```

对于 four 和 six，它们的类型都是 IpAddrKind，但又各自唯一。

```rs
fn route(ip_kind: IpAddrKind) {}
```

这在作为函数参数类型时能够体现出来。

```rs
route(IpAddrKind::V4);
route(IpAddrKind::V6);
```

## 枚举使用

```rs
enum IpAddrKind {
    V4,
    V6,
}

struct IpAddr {
    kind: IpAddrKind,
    address: String,
}

let home = IpAddr {
    kind: IpAddrKind::V4,
    address: String::from("127.0.0.1"),
};

let loopback = IpAddr {
    kind: IpAddrKind::V6,
    address: String::from("::1"),
}
```

```rs
enum IpAddr {
    V4(String),
    V6(String),
}

let home = IpAddr::V4(String::from("127.0.0.1"));
let loopback = IpAddr::V6(String::from("::1"));
```

这样简写，无需声明结构体，这里的枚举像是一个函数。

```rs
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

let home = IpAddr::V4(127, 0, 0, 1);

let loopback = IpAddr::V6(String::from("::1"));
```

```rs
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}
```

这样的枚举声明类似于以下定义四个结构体的方式

```rs
struct QuitMessage; // unit struct
struct MoveMessage {
    x: i32,
    y: i32,
}
struct WriteMessage(String); // tuple struct
struct ChangeColorMessage(i32, i32, i32); // tuple struct
```

## 枚举对比结构体

```rs
impl Message {
    fn call(&self) {

    }
}

let m = Message::Write(String::from("hello"));
m.call();
```

## 选项枚举

Option 是标准库内定义的类型，可用于表示某个值可能是某个值或者什么都不是。

比如当你访问一个非空列表的第一项时，你得到的就是列表第一项的值。

当你访问一个空列表的第一项时，你得到的就是什么都不是。

这种类型系统术语中的概念意味着编译器发现你处理了所有你应该处理的情况。

编程语言的设计一般会考虑你需要包含哪些功能，但那些未被包含的功能同样重要。Rust 不像其他编程语言，它没有 null 值。

null 值的问题在于你可能把它当做非 null 值使用，这样你会得到意想不到的错误。

```rs
enum Option<T> {
    None,
    Some<T>,
}
```

选项枚举的价值在于当某个值的类型是选项枚举时，你需要处理它为 null 的情况，而在非 null 的情况你可以放心的使用。

# 模式匹配

模式匹配允许你在值匹配到各个条件时执行基于该值情况的逻辑，模式可以是字面量、变量名或通配符等。匹配的强大之处在于模式的可表达性以及编译器可以确保所有情况都会被处理。

> 类似 JS 的 switch 语句

```rs
#[derive(Debug)]
enum UsState {
    Alabama
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
        Coin: Quarter(state) => {
            println!("State quarter from {:?}!", state);
            25
        },
    }
}
```

```rs
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(i) => Some(i + 1),
    }
}

let five = Some(5);
let six = plus_one(five);
let none = plus_one(None);
```

## 匹配需要完备

当匹配条件未声明完备（处理所有情况）时，编译器会报错并提示未处理的情况。

```rs
let dice_roll = 9;
match dice_roll {
    3 => add_fancy_hat(),
    7 => remove_fancy_hat(),
    other => move_player(other),
}

fn add_fancy_hat() {}
fn remove_fancy_hat() {}
fn move_player(num_spaces: u8) {}
```

这里的 other 表示除已经定义的匹配外，剩余的情况都会走到这里，这种情况可以任意定义匹配名称，比如这里使用了 other。

如果不想显式地声明剩余情况，可以使用“_”表示“其他情况”，然后声明对应的处理函数或者什么都不做

```rs
let dice_roll = 9;
match dice_roll {
    3 => add_fancy_hat(),
    7 => remove_fancy_hat(),
    _ => reroll(),
}

fn add_fancy_hat() {}
fn remove_fancy_hat() {}
fn reroll() {}
```

```rs
let dice_roll = 9;
match dice_roll {
    3 => add_fancy_hat(),
    7 => remove_fancy_hat(),
    _ => (),
}

fn add_fancy_hat() {}
fn remove_fancy_hat() {}
```
