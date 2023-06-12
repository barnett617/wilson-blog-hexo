---
title: java零碎知识点整理
tags:
  - java
date: 2017-02-07 17:27:54
update: 2017-02-07 17:27:54
summary:
categories: 笔记
---

> 字符串相关方法、数据类型转换、正则表达式

```java
package com.test.wx.teststr;

import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class TestString {
	
	public static void intToString() {
		int i = 10;
		Integer j = 11;
		int k = 12;
		Integer l = 13;
		String iStr = String.valueOf(i);
//		String iStr2 = (String)j;
//		String iStr3 = (String)k;
		String iStr4 = String.valueOf(l);
		System.out.println(iStr);
		System.out.println(iStr4);
	}
	
	public static void indexOfTest() {
		String quote = "An *onion* a day keeps everyone away!";
		int startBound = quote.indexOf("*");
		int endBound = quote.lastIndexOf("*");
		System.out.println(startBound+" "+endBound);
		System.out.println(quote.substring(startBound, endBound));
	}
	
	public static void regexTest(String input) {
		/**
		 * java中对反斜杠解释为转义符号
		 * 所以制定正则字符串时，要对反斜杠做转义处理
		 * \d{n}表示n个数字，要多加一个\以使\d的反斜杠生效
		 * \\+匹配加号
		 * +在正则中有专属含义，表示匹配其前面的内容1-n次
		 * 若要匹配加号本身，需要对加号进行正则的转义处理（即加反斜杠），
		 * 若要表示转义，需要对反斜杠进行java的转义处理（即加反斜杠）
		 */
		String regex = "\\+\\d{2}-\\d{11}";		// true
//		String regex = "";
//		String regex = "\\bcat\\b";
//		String regex = "+\d{2}-d{10}";
//		String regex = "\b+\d{2}-d{10}\b";
//		String regex = "";
		Pattern pattern = Pattern.compile(regex);
		Matcher matcher = pattern.matcher(input);
		boolean result = matcher.matches();
		System.out.println(result);
	}
	
 	public static void main(String[] args) {
// 		intToString();
// 		indexOfTest();
 		regexTest("+86-17355159636");		// true
	}

}

```

> final关键字修饰函数参数

```java
package com.test.wx.testfinal;

public class TestKwFinal {
	
	public static int i;

	public static void testBaseFinal() {
		i = 3;
		System.out.println(i);
		finalSet(i);
		System.out.println(i);
	}
	
	public static void testFinalReference() {
		User user = new User();
		user.setId(1);
		user.setName("Tom");
		System.out.println(user.getId()+" "+user.getName());
		finalRefSet(user);
		System.out.println(user.getId()+" "+user.getName());
	}
	
	/**
	 * 基本类型的值在方法内部不能被改变
	 * @param i
	 */
	public static void finalSet(final int i) {
//		i = 10;
	}
	
	/**
	 * 所指向的引用未被改变，依然是User，但是引用的属性值可以被改变
	 * @param user
	 */
	public static void finalRefSet(final User user) {
//		user = new User();
		user.setName("Jerry");
	}
	
	public static void main(String[] args) {
//		testBaseFinal();
		testFinalReference();
	}
}

```