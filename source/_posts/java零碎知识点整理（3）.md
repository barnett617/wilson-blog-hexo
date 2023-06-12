---
title: java零碎知识点整理（3）
date: 2017-08-16 20:16:01
update: 2017-05-08 20:16:01
tags: 
 - java
categories: 后端
---

> 关于java字符串String以及Hashcode的一些整理
> 
> hashcode即是对象地址的十进制形式，而equals方法即是在比较两个对象的hashcode是否相等

<!--more-->

```java
package com.test.wx.testequals;

public class TestEquals {
	
	public static void testEquals() {
		String a = "a";
		String b = "a";
		boolean result = a.equals(b);
		System.out.println(result);			// true
		System.out.println(a.hashCode() == b.hashCode());		// true
	}
	
	public static void testEqual() {
		String a = "a";
		String b = "a";
		if(a == b) {						// true
			System.out.println("true");
			System.out.println(a.hashCode() == b.hashCode());
		}else {
			System.out.println("false");
		}
	}
	
	public static void testEquals2() {
		String a = "a";
		String b = a;
		// equals方法比较两个对象的hashcode,hashcode即对象的地址
		System.out.println(a.equals(b));
		// == 比较两个变量或者对象的值是否相等，equals比较两个对象的引用是否相等（是否指向同一个对象）
		System.out.println(a==b);
	}
	
	public static void normalTest() {
		int a = 12;
		String b = String.valueOf(a);
	}
	
	public static void testObjectHashcode() {
		User user1 = new User(1, "test");
		User user2 = new User(2, "test");
		System.out.println("user1 toString: "+user1);
		System.out.println("user1 hashcode: "+user1.hashCode());
		// hashcode即是对象地址的十进制形式
		System.out.println(Integer.valueOf("659e0bfd", 16));
		System.out.println(user2);
	}
	
	public static void hexToDeciaml() {
		long a = 1021653256;
		System.out.println(Long.toHexString(a));	//3ce53108
		System.out.println(Integer.valueOf("659e0bfd", 16));
	}

	public static void main(String[] args) {
//		testEquals();
//		testEqual();
//		testEquals2();
//		hexToDeciaml();
		testObjectHashcode();
	}
	
}

```

> 枚举enum整理
> 
> 枚举enum是一种类型，不是类，所以不能被实例化，一般用来定义集合常量

```java
package com.test.wx.testenum;

public class EnumTest {

	PrinterType printType;
	enum PrinterType { INKJET, DOTMATRIX, LASER };
	public EnumTest(PrinterType pType) {
		printType = pType;
	}
	
	public static void main(String[] args) {
//		PrinterType pType = new PrinterType();
		EnumTest enumTest = new EnumTest(PrinterType.LASER);
		System.out.println(enumTest.printType);
	}
}

```
