---
title: 通过类比其他编程语言的方式学习Rust语法-02
date: 2023-07-10 23:39:34
tags: [rust]
---

# Rust学习第二篇

<!-- more -->

# 结构体

使用结构体可以自定义一系列数据结构并打包在一起，然后用其定义多个符合这个结构的值。

> 类比 C 语言的结构体以及 TypeScript 的 interface

结构体类似面向对象编程中的对象属性声明，使用结构体可以声明并实例化数据对象。

可被用于定义有关联的函数，这种有关联的函数称为”方法“。

结构体和枚举被用来创建新类型的代码块，以此充分利用 Rust 在编译时进行的类型检查。

## 声明和初始化结构体

结构体类似于元组，都包含多个相关的值，并且支持存放不同类型的值。

不同于元组的是，结构体需要给每个值进行命名，因此可以知道每个值的含义。

增加了对于值的命名也意味着结构体比元组更加灵活——无需再依赖值的顺序来访问实例内的值。

### 结构体声明

```rs
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}
```

> 类比 C 语言结构体的声明方式

### 结构体实例化

实例化结构体时无需按照结构体内的变量顺序，这也意味着结构体类似于数值类型的通用”模板“

```rs
fn main() {
    let user1 = User {
        active: true,
        username: String::from("someusername123"),
        email: String::from("someone@example.com"),
        sign_in_count: 1,
    }
}
```

### 结构体访问和赋值

```rs
fn main() {
    let user1 = User {
        active: true,
        username: String::from("someusername123"),
        email: String::from("someone@example.com"),
        sign_in_count: 1,
    }
    user1.email = String::from("anotheremail@example.com");
}
```

### 从已有实例生成新实例

```rs
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}

fn main() {
    let user1 = User {
        email: String::from("someone@example.com"),
        username: String::from("someusername123"),
        active: true,
        sign_in_count: 1,
    };

    let user2 = User {
        active: user1.active,
        username: user1.username,
        email: String::from("another@example.com"),
        sign_in_count: user1.sign_in_count,
    }
}
```

对于 user2 和 user1 这种结构相同，只有部分字段更新的情况，可以使用 .. 对相同字段的值进行简写

```rs
fn main() {
    let user2 = User {
        email: String::from("another@example.com"),
        ..user1
    };
}
```

> .. 类似于 JS 中的剩余参数，只能用于最后

## 元组结构体

元组结构体使用 struct 关键字声明，可以对结构相同的元组各自进行命名，从而进行区分，用于表示不同的内容

```rs
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

fn main() {
    let black = Color(0, 0, 0);
    let origin = Point(0, 0, 0);
}
```

black 和 origin 的结构相同，却是不同的自定义数据类型。比如一个函数使用 Color 作为参数类型，则不能将 Point 类型的实例作为参数传入。

> 类似于 TS 里的类型，即使结构相同，但属于不同的类型

# 方法

“方法”类似“函数”，都使用 fn 关键字进行声明，可以有参数和返回值。

方法与函数的区别在于方法只能在结构体、枚举或者特征对象内部定义，并且第一个参数始终为 self，表示方法被调用时的实例（上下文）。

> 类似于 python 和 java 的方法

## 声明方法

```rs
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!(
        "The area of the rectangle is {} square pixels.",
        rect1.area()
    );
}
```

这里使用了 impl 关键字，用于表示与类型的关联的实现。&self 其实是 &self: Self 的简写，Self 表示 impl 所实现的类型。

self 既可以传递所有权(self)，也可以借用不可变引用 (&self) 或借用可变引用 (&mut self)，和函数的参数用法类似。

> 这里我们只希望读取值，而不进行值的写操作，因此使用了借用不可变引用，而非可变引用或传递所有权。

## 方法对比函数

之所以使用方法而非函数，是为了在多个地方能够访问 self，而无需重复作为参数传递，这样组织代码更加方便。

方法名可以和结构体内的字段同名

```rs
impl Rectangle {
    fn width(&self) -> bool {
        self.width > 0
    }
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    if rect1.width() {
        println!("The rectangle has a nonzero width; it is {}", rect1.width);
    }
}
```

这里既访问了实例的 width 方法，也访问了实例的 width 属性。当使用括号访问时，表示在访问方法，没有括号时表示在访问属性。

### getter

某些情况，我们希望当存在方法与属性同名时，访问方法仅仅是返回同名的属性值，这种方法称为“getter”

> getter 不会被默认实现

getter 一般用于将一个结构体的属性不对外暴露（私有化），而只暴露方法（公有化）

> 开闭原则——对访问开放，对修改封闭
