---
title: Java基础算法（插入、选择、冒泡、快排）
tags:
  - java
date: 2016-12-15 23:20:37
update: 2016-12-15 23:20:37
summary:
categories: 算法
---

### 一、直接插入排序
#### 1、原理：从待排序的数中选出一个来，插入到前面的合适位置。
#### 2、适用场景：基本有序的数据
#### 3、代码实现：

```java
	static int data[] = {12, 6, 17, 108, 97, 93, 256, 1024};
	
	public static void insertSort() {
		int tmp, i = 0;
		// 遍历数组
		for(int j = 0; j<data.length; j++) {
			// 依次取出每个数放入tmp
			tmp = data[j];
			// 将取出的数与之前面一个比较
			i = j - 1;
			// 若小于前面一个数，则将前面一个数后移（覆盖取出来的这个数的位置）
			while(i >= 0 && tmp < data[i]) {
				data[i+1] = data[i];
				// 继续和再前面一个数比较
				i--;
			}
			// 将取出的数插入
			data[i+1] = tmp;
			System.out.println();
			print();
		}
	}
```

### 二、选择排序
#### 1、原理：与直接插入排序正好相反，选择排序是从待排序的数中选出**最小**的放在已经排好的**后面**，这个算法**选数耗时**
#### 2、代码实现：

```java
	static int data[] = {12, 6, 17, 108, 97, 93, 256, 1024};
	
	public static void selectSort() {
		// i, j, k未初始化
		int i, j, k, tmp = 0;
		// 遍历数组，从i开始的length-1个数
		for(i=0; i<data.length-1; i++) {
			// 设置k的值和i一致
			k = i;
			// 遍历数组，从i+1开始的length-1个数
			for(j=i+1; j<data.length; j++) {
				// 如果后一个数比前面一个数小
				if(data[j] < data[k]) {
					// 则把后一个数的坐标赋予k（保持k坐标的数为当前轮最小数）
					k = j;
				}
			}
			// 若发生后一个数比前一个数小的情况，则交换
			if(k != i) {
				// 交换
				tmp = data[i];
				data[i] = data[k];
				data[k] = tmp;
				System.out.println();
				print();
			}
		}
	}
```

### 三、冒泡排序
#### 1、原理：小的数一点一点向前起泡，最终有序。
#### 2、代码实现：

```java
	static int data[] = {12, 6, 17, 108, 97, 93, 256, 1024};
	
	public static void bubbleSort() {
		int i, j, tmp = 0;
		// 遍历从i开始的length-1个数
		for(i=0; i<data.length - 1; i++) {
			// 依次比较相邻两个数
			for(j=data.length - 1; j>i; i--) {
				// 若后者小于前者，则换位
				if(data[j] < data[j-1]) {
					tmp = data[j];
					data[j] = data[j-1];
					data[j-1] = tmp;
				}
			}
		}
	}
```
### 四、快速排序
#### 1、原理：设置两个指针：i和j，分别指向第一个和最后一个，i像后移动，j向前移动，选第一个数为标准（一般这样做，当然快排的关键就是这个“标准”的选取），从后面开始，找到第一个比标准小的数，互换位置，然后再从前面，找到第一个比标准大的数，互换位置，第一趟的结果就是标准左边的都小于标准，右边的都大于标准（但不一定有序），分成两拨后，继续递归的使用上述方法，最终有序！
#### 2、适用场景：基本**无序**的数据
#### 3、代码实现：

```java
	public static void quickSort(int data[], int start, int end) {
		int i, j;
		i = start;
		j = end;
		if((data == null) || (data.length == 0)) {
			return;
		}
		
		while(i < j) {
			// 以start下标的数据为key，从右向左依次扫描
			while(i < j && data[i] <= data[j]) {
				j--;
			}
			// 找出第一个比key小的，交换位置
			if(i < j) {
				int temp = data[i];
				data[i] = data[j];
				data[j] = temp;
			}
			while(i < j && data[i] < data[j]) {
				// 左侧扫描（此时a[j]存着key的值）
				i++;
			}
			// 找出第一个比key大的，交换位置
			if(i < j) {
				int temp = data[i];
				data[i] = data[j];
				data[j] = temp;
			}
		}
		// 递归调用，把key前面的完成排序
		if(i-start > 1) {
			quickSort(data, 0, i-1);
		}
		// 递归调用，把key后面的完成排序
		if(end-j > 1) {
			quickSort(data, j+1, end);
		}
	}
```