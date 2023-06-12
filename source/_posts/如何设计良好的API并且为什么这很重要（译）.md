---
title: 如何设计良好的API并且为什么这很重要（译）
date: 2018-01-16 11:38:31
update: 2018-01-16 11:38:31
categories: 翻译
tags: API
---

### 为什么API设计很重要？

本文翻译自Effective Java作者Joshua Bloch撰写的一篇关于API设计的分享

<!--more-->

#### API可以是公司最大的资产之一

- 客户投入巨资：购买、撰写、学习（售前一系列培训）
- 停止使用API导致的花费令人望而却步（如果不使用API可能要花费更多）
- 成功的公共API赢得客户

#### 也可以成为公司最大的负债之一

- 糟糕的APIs将导致无休止的电话技术支持（需要接听很多来自客户的反映）

#### 公共的APIs是永久的——一次去做正确事情的机会

### 为什么API设计对于你来说很重要

#### 如果你编码，你就是一个API的设计者

- 良好的代码应该是模块化的——每一个模块都有一个API
- 有用的模块往往被重用

#### 对于API方面的思考将提高代码的质量

### 好的API所具备的特征

- 易于学习
- 易于使用，甚至无需任何文档
- 不易误用
- 易于阅读并且对所使用的代码部分易于维护
- 足够强大以满足需求
- 易于拓展
- 适用于大众

### 大纲

- API设计的过程
- 一般原则
- 类设计
- 方法设计
- 异常设计
- API重构设计

## I API设计的过程

### 收集需求——以一种健康的程度怀疑

#### 通常你会得到建议的解决方法作为替代方案

- 可能存在更好的解决方案

#### 你的工作是去提取出真正的需求

- 应采取用例的形式
- 可以更容易、更有意义地建立更普遍的东西

#### 从简短的规范页开始比较理想

- 在这个阶段，敏捷胜过完整性
- 和尽可能多的人谈（原文：Bounce spec off as many people as possible）
	倾听他们的输入并认真对待
- 如果你保持规范简短，那么将易于修改
- 充分自信
	这涉及到编码时也很必要

### 尽早写给你的API

#### 以下应开始于在你实现API之前

- 从你将扔掉的实现中拯救你

#### 以下应开始于你正确指定出API之前

- 从撰写你将扔掉的规范中拯救你

#### 继续写API因为它充实你

- 避免令人讨厌的惊喜
- 代码作为例子、单元测试而存在

### 关于SPI的内容甚至更重要

#### 服务提供接口（Service Provider Interface, SPI）

- 插件式接口使得实现多样性
- 例如：Java加密拓展接口（Java Cryptography Extension, JCE）

#### 在发布之前编写多个插件

- 如果你只写一个，它可能不支持另一种情况
- 如果你写两个，它会很难支持更多
- 如果你写三个(原文three应表示多个?)，它会良好工作

#### Will Tracz称之为“三项法则”
（曾经以为程序销售员Addision-Wesley,1995的自白）

### 保持现实的期望

#### 大多数API设计的过约束

- 你不必取悦每一个人
- 旨在平等地使每个人都感到高兴

#### 期望犯错

- 几年真实世界的使用将冲洗它们
- 期望发展API

## II 一般原则

### API应该只做一件事并把它做好

#### 功能应该易于解释

- 如果很难命名API，那它通常是一个坏讯号
- 良好的命名会驱动开发
- 要易于分割和合并

#### API应尽可能的小但不能再小

#### API应该满足它的初始需求

#### 当存疑时就抛弃掉

- 函数、类、方法、参数等等
- 你总可以加些什么，但你永远不能去掉什么

#### 概念的重量比实体块的重量更重要

#### 寻找一个好的力量/重量比率（此处应指API的作用和轻重之比）

### 实现不应该影响API

### 实现细节

- 迷惑用户
- 禁止掉改变API实现的自由

#### 意识到实现细节是什么

- 不要过度指定方法的行为
- 例如：不要指定散列函数
- 所有的可调整参数都是可疑的

#### 别让实现细节“泄露”进API

- 磁盘上的格式和线上的格式例外

### 最小化对于所有的可达性

#### 将类和方法指定得尽可能私有

#### 公共类不应该有公共域（除了常量）

#### 这最大化了信息隐藏

#### 允许模块被独立使用、理解、构建、测试、调试

### 给API命名的事务相当于一种小语言

#### 名字应大部分不言自明（自解释的）

- 避免模糊的缩写

#### 保持一致——同样的词应表达同样的意思

- 贯穿API的整个内容（包括不同平台上的该API）

#### 定期争取对称

#### 代码应该读起来像散文

```
if (car.speed() > 2 * SPEED_LIMIT)
		generateAlert("Watch out for cops!");
```

### 文档相关事宜

> 重用是一件说起来容易做起来难的事。具体做起来既需要好的设计又需要良好的文档。即使我们看到好的设计，我们仍很少能看到没有好的文档组件被重用。

> D.L.Parnas, _Software Aging. Proceedings of 16th International Conference Software Engineering, 1994


### Document Religiously

#### 为每一个类、接口、方法、构造器、参数和异常制作文档（注释）

- 类：实例所表示的东西
- 方法：方法和客户之间的契约
	- 先决条件、后置条件、副作用
- 参数：提示性的单位、格式、所有权

#### 文档要非常认真地陈述

### 考虑API设计决定的性能后果

#### 糟糕的决定会限制性能

- 让类型易变
- 提供构造器以取代静态工厂
- 使用实现类型取代接口

#### 不要扭曲API来获得性能

- 底层性能问题会被修复，但头疼的事会一直伴随着你
- 良好的设计通常与良好的性能相吻合

#### API设计决策在性能方面的影响是真实并且永久的

- Component.getSize() returns Dimension
- 尺寸是易变的
- 每一个getSize调用都必然分配Dimension
- 将导致非常多不必要的对象分配
- 在1.2版本增加替代品，老的客户端代码仍然慢（在新的版本找到解决方案，但不能解决旧代码的性能问题）

### API必须和平台和平共存

#### 做习惯性的事

- 遵守标准的命名约定
- 避免过时的参数和返回类型
- 模仿核心APIs和语言中的模式

#### 利用API友好功能

- 泛型、可变参数、枚举、默认参数

#### 了解并避免API陷阱

- 常量（Finalizers）、公共静态常量数组

## III 类设计

### 最小化可变性

#### 类应该是不可变的除非有一个好的理由不去这么做

- 优点： 简洁、线程安全、可复用
- 缺点： 对于每个值都分离了对象

#### 如果可变，保证状态空间尽可能小并被良好定义

- 搞清何时去调用哪一个方法是合法的

#### 仅在合理的地方建子类

##### 建子类按时可替代性（Liskov）

- 子类仅当is-a关系时存在
- 否则，使用组合

##### 公共类不应该再包含其他公共子类，以保证实现简单

反例： Properties extends Hashtable
			 Stack extends Vector

正例： Set extends Collection

### 为继承做设计和文档否则禁止

#### 继承违反封装（Snyder, 86）

- 子类对于父类的实现细节敏感

#### 如果你允许建子类，那么就文档自用

- 方法如何相互使用

#### 保守策略：所有具体的类都不可变（final）

反例：J2SE包中许多的具体类
正例：AbstractSet, AbstractMap

## IV 方法设计

### 别让客户端把模板能做的事都做了

#### 减少对样板代码的需要

- 通常经cut-and-paste完成
- 丑陋、恼人并且易错

### 不要违反最小原则

#### API的使用者不应该对于某些行为感到惊讶

- 值得额外的实现努力
- 这甚至值得降低性能

```
public class Thread implements Runnable {
		// Tests whether current thread has been interrupted
		// Clears the interrupted status of current thread.
		public static boolean interrupted();
}
```

上面的第二行注释所述的功能就做了额外的没必要的努力，违背了最小原则

### 当错误发生尽可能快地产生错误报告

#### 编译时报错最佳——静态拼写、generics

#### 在运行时，第一个方法调用失败为最佳

- 方法应该是原子性失败（failure-atomic）

```
// A Properties instance maps strings to strings
public class Properties extends Hashtable {
		public Object put(Object key, Object value);

		// Throws ClassCastException if this Properties
		// contains any keys or values that are not strings
		public void save(OutputStream out, String comments);
}
```

### 为所有的数据访问提供字符串的形式的编程接口

#### 否则客户端需要做字符串转换

- 对于客户端来说很痛苦
- 更糟糕的是，turns strings format into de facto API（无力翻译orz）

```
public class Throwable {
		public void printStackTrace(PrintStream s);
		public StackTraceElement[] getStackTrace();		// Since 1.4
}

public final class StackTraceElement {
		public String getFilaName();
		public int getLineNumber();
		public String getClassName();
		public String getMethodName();
		public boolean isNativeMethod();
}
```

### 小心重载

#### 避免模糊的重载

- 多个重载适用于相同的情况
- 保守策略：没有两个重载拥有同样多的参数个数

#### 仅仅是因为你可以但不意味着你应当

- 通常最好使用不同的名字

#### 如果你必须提供模糊的重载，请确保相同的参数拥有相同的行为

反例:
```
public TreeSet(Collection c);		// Ignores order
public TreeSet(SortedSet s);		// Respects order
```

### 使用恰当的参数和返回类型

#### 赞成接口类型作为类的输入

- 提供灵活性、性能

#### 使用最具体的可能的输入参数类型

- 把运行时错误提前到编译时

#### 如果存在更好的类型请别用String类型

- Strings是繁琐的、易错的并且慢的

#### 不要将浮点数用于货币值

- 二进制浮点会导致不精确的结果

#### 使用double(64位)优于float(32位)

- 精确度损失是真实的，性能损失是可忽略的

### 在方法之间使用一致的参数顺序

#### 如果参数类型相同，尤其重要

反例：
```
#include <string.h>
char *strcpy (char *dest, char *src);
void bcopy 	 (void *src, void *dst, int n);
```

正例：
java.util.Collections——第一个参数总被收集来用于修改或查询

java.util.concurrent——time总被指定为long delay, TimeUnit unit

### 避免长参数列表

#### 三个或更少参数是比较理想的

- 如果存在更多，用户则需参阅文档

#### 相同类型的长参数列表是有害的

- 程序员会错误地转置参数
- 程序仍会编译、运行，但行为不端

#### 两种缩短参数列表的技巧

- 拆分方法
- 创建辅助类来保存参数

反例：
```
// Eleven parameters including four consecutive ints
HWND CreateWindow(LPCTSTR lpClassName, LPCTSTR lpWindowName,
			DWORD dwStyle, int x, int y, int nWidth, int nHeight,
			HWND hWndParent, HMENU hMenu, HINSTANCE hInstance,
			LPVOID lpParam);
```

### 避免返回值需要异常处理

#### 返回零长度数组或空集合而非null

```
package java.awt.image;
public interface BufferedImageOp {
		// Returns the rendering hints for this operation,
		// or null if no hints have been set.
		public RenderingHints getRenderingHints();
}
```

## V.异常设计

### 抛出异常以表明异常的条件

#### 不要强迫客户端去使用异常来控制流

反例:

```
private byte[] a = new byte[BUF_SIZE];
void processBuffer (ByteBuffer buf) {
	try {
		while (true) {
			buf.get(a);
			processBytes(tmp, BUF_SIZE);
		}
	} catch (BufferUnderflowException e) {
		int remaining = buf.remaining();
		buf.get(a, 0, remaning);
		processBytes(bufArray, remaining);
	}
}
```

#### 反过来，不要安静的失败

例如：

```
ThreadGroup.enumerate(Thread[] list)
```

### 赞成未经检查的异常

#### 已检查——客户端必须采取修复措施
#### 未检查——程序报错
#### 过度使用已检查的异常会导致样板化

反例：

```
try {
		Foo f = (Foo) super.clone();
		...
} catch (CloneNotSupportedException e) {
		// This can't happen, since we're Cloneable
		throw new AssertionError();
}
```

### 在异常中包含错误捕获信息

#### 允许诊断、修复或恢复

#### 对于未检查的异常，信息就足够了

#### 对于已检查的异常，提供访问者

## VI 重构API设计

### 向量的子列表操作

```
public class Vector {
		public int indexOf(Object elem, int index);
		public int lastIndexOf(Object elem, int index);
}
```

#### 不够强大——只支持搜索

#### 没有文档很难使用

### 字列表操作重构

```
public interface List {
		List subList(int fromIndex, int toIndex);
		...
}
```

#### 非常强大——支持所有的操作

#### 接口的使用减少了概念的重量

- 高功率重量比

#### 没有文档也易于使用

### Thread-local变量

```
// Broken - inappropriate use of String as capability.
// Keys constitue a shared global namespace.
public class ThreadLocal {
		private ThreadLocal() { }		// Non-instantiable

		// Sets current thread's value for named variable.
		public static void set(String key, Object value);

		// Returns current thread's value for named variable.
		public static Object get(String key);
}
```

#### Thread-Local变量重构（1）

```
public class ThreadLocal {
		private ThreadLocal() { }  // Noninstantiable

		public static class Key {	Key() { } }
		// Generates a unique, unforgeable key
		public static void set(Key key, Object value);
		public static Object get(Key key);
}
```

### 有效，但是需要使用样板代码

例如:

```
static ThreadLocal.Key serialNumberKey = ThreadLocal.getKey();
ThreadLocal.set(serialNumberKey, nextSerialNumber());
System.out.println(ThreadLocal.get(serialNumberKey));
```

#### Thread-Local变量重构（2）

```
public class ThreadLocal {
		public ThreadLocal() { }
		public void set(Object value);
		public Object get();
}
```

#### 消除API和客户端代码之间的混乱

```
static ThreadLocal serialNumber = new ThreadLocal();
serialNumber.set(nextSerialNumber());
System.out.println(serialNumber.get());
```

## 结论

### API设计是一种高贵且有益的工艺

- 改进了很多程序员、最终用户和公司

### 这次谈话涵盖了一些启发式的手艺

- 不要狂妄地坚持，但...
- 不要没有理由的违反

### API设计很难

- 不是一个孤独的活动
- 完美是无法实现的，但无论如何都要尝试

## 个人评价&总结

本文评价了很多Java语言包以及面向对象的优点和缺点，也包含C语言的部分例子。本文作者Joshua Bloch同时也是Effective Java的作者。本文中有些用词比较晦涩难懂，但通篇整体很好也很全面地剖析了API设计的重要性及一部分技巧和注意事项，值得参考借鉴。

翻译过程也是磕磕绊绊，很多地方自我感觉翻译的不甚准确，甚至有问题，实在找不到合适的翻译的地方注明了原文或保留原文词句，希望有心读者能指正并反馈。不过翻译的过程的确为了理解作者原意，也会竭尽脑力去思考和体会字里行间所表达的思想，收益良多。
其中不乏有些建议的确出现于日常的编程实践，并作为编程规范存在于最佳实践中，一眼就能理解。也有一些目前可能尚未在实际应用中良好实践。

原文链接：<a href="https://web.archive.org/web/20110903030015/http://lcsd05.cs.tamu.edu/slides/keynote.pdf">https://web.archive.org/web/20110903030015/http://lcsd05.cs.tamu.edu/slides/keynote.pdf</a>
