---
title: 反转数组
date: 2020-01-15 20:31
tags:
---

```js
/**
 * [ 吃瓜][ 加油][ 汗][ 天啊][ Emm][ 社会社会][ 旺柴][ 好的][ 打脸][ 哇]
 * 这样一个字符串能不能去掉其中所有的空格
 */
function removeBlanks(target) {
  console.log('处理前：' + target);
  const result = target.replace(/\s/g, '');
  console.log('处理后：' + result);
  console.log('原数据：' + target);
}

/**
 * [吃瓜][加油][汗][天啊][Emm][社会社会][旺柴][好的][打脸][哇]
 * 能不能把顺序调过来变成
 * [哇][打脸][好的][旺柴][社会社会][Emm][天啊][汗][加油][吃瓜]
 * 空间复杂度较差，使用了两个栈
 */
function revert(target) {
  console.log('处理前：' + target);
  let stack = [];
  let newStack = [];
  target.split('').forEach((char) => {
    // 识别到关闭符且栈中有开始符则出栈
    if (char === ']' && stack.includes('[')) {
      stack.push(char);
      // 将字符组装成字符串，从二维数组头部装入
      newStack.unshift(stack.join(''));
      // 关键步骤：清空栈，否则每一次向二维数组装入时都冗余之前装过的数据
      stack.length = 0;
    } else {
      stack.push(char);
    }
  })
  let result = '';
  if (newStack.length > 0) {
    result = newStack.join('');
  }
  console.log('处理后：' + result);
  console.log('原数据：' + target);
}

// 程序入口
function main() {
  var str = '[ 吃瓜][ 加油][ 汗][ 天啊][ Emm][ 社会社会][ 旺柴][ 好的][ 打脸][ 哇]';
  // removeBlanks(str);
  // 处理前：[ 吃瓜][ 加油][ 汗][ 天啊][ Emm][ 社会社会][ 旺柴][ 好的][ 打脸][ 哇]
  // 处理后：[吃瓜][加油][汗][天啊][Emm][社会社会][旺柴][好的][打脸][哇]
  // 原数据：[ 吃瓜][ 加油][ 汗][ 天啊][ Emm][ 社会社会][ 旺柴][ 好的][ 打脸][ 哇]
  var str2 = '[吃瓜][加油][汗][天啊][Emm][社会社会][旺柴][好的][打脸][哇]';
  revert(str2);
  // 处理前：[吃瓜][加油][汗][天啊][Emm][社会社会][旺柴][好的][打脸][哇]
  // 处理后：[哇][打脸][好的][旺柴][社会社会][Emm][天啊][汗][加油][吃瓜]
  // 原数据：[吃瓜][加油][汗][天啊][Emm][社会社会][旺柴][好的][打脸][哇]
}
main();
```