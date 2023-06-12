---
title: java零碎知识点整理（2）
date: 2017-08-16 20:19:19
update: 2017-05-07 20:19:19
tags: 
 - java
categories: 后端
---

> 构造器（构造方法）
> 在类实例化时被调用，对实例对象进行初始化

<!--more-->

```java
package com.test.wx.testinit;

public class Color {

	int red, green, blue;
	/**
	 * 以下这个方法不是构造器，构造器没有“void”这个返回值
	 */
	void Color() {
		red = 10;
		green = 10;
		blue = 10;
	}
	
	/**
	 * 这才是正确的构造器，会在类实例化一个对象时被调用，对对象进行初始化
	 */
	Color() {
		red = 10;
		green = 10;
		blue = 10;
	}
	
	void printColor() {
		System.out.println("red:"+red+"green:"+green+"blue:"+blue);
	}
	
	public static void main(String[] args) {
		Color color = new Color();
		color.printColor();		// red:0green:0blue:0
	}
	
}

```

> 修饰符（关键字）
> final/abstract
> abstract不能与final同时使用，**abstract**类为抽象类，包含抽象的方法待实现，需要其他类**继承**该类，对**抽象方法**进行具体实现，而**final**修饰的类表示该类**不可被继承**，二者相违背。


