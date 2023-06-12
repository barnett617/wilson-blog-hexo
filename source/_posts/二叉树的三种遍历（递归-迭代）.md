---
title: 二叉树的三种遍历（递归+迭代）
tags:
  - 算法
date: 2020-04-16 17:10:48
update: 2020-04-16 17:10:48
summary:
categories: 算法
---

二叉树的三种遍历（递归+迭代）

<!--more-->

## 前序遍历

```js
/**
 * @param {TreeNode} root
 * @return {number[]}
 */
var preorderTraversal = function(root) {
    // 迭代法
    var stack = [];
    var ans = [];
    if (root && root.val) {
        stack.push(root);
        // 利用栈临时存取元素
        while (stack.length > 0) {
            // 从栈顶取出当前元素
            var cur = stack.pop();
            if (cur !== null) {
                ans.push(cur.val);
                // 栈先进后出，所以先放右子树
                if (cur.right) stack.push(cur.right);
                if (cur.left) stack.push(cur.left);
            }
        }
    }
    return ans;
};
```

## 中序遍历

```js
/**
 * @param {TreeNode} root
 * @return {number[]}
 */
var inorderTraversal = function(root) {
    // 迭代
    var ans = [];
    var stack = [];
    var cur = root;
    // cur条件是为了启动循环，因为初始时栈为空
    while (cur || stack.length) {
        // 所有左子树依次入栈（根->左->根->左……直到没有左子树了，根就可以出栈了）
        while (cur) {
            // 只有这里有入栈操作，就是判断当前子树是否有左子树
            stack.push(cur);
            cur = cur.left;
        }
        // 出栈的是无左子树的节点,因为没有左子树入栈才会走到这里
        cur = stack.pop();
        ans.push(cur.val);
        // 没有左子树，并且根被记录后右子树入栈（整棵树的右子树——整体左根右）
        cur = cur.right;
    }
    return ans;
    // 内层循环在判断左子树的存在
    // 外层循环在判断右子树的存在
    // 根是否出栈取决于节点是否还有左子树，因为顺序为左根右
    // 当节点没有左子树时可以出栈根
    // 当无右子树,但栈未空时,是当前节点处理完毕,向父节点前进
};
```

## 后序遍历

```js
/**
 * @param {TreeNode} root
 * @return {number[]}
 */
var postorderTraversal = function(root) {
    // 迭代
    var stack = [], ans = [], cur = root;
    while (cur || stack.length) {
        // 整体顺序是依次将所有左子树入栈、所有右子树入栈
        // 右子树入栈取决于栈顶元素是否有右子树
        // 因为只有当cur有值时才会发生入栈操作
        // 而当栈顶元素存在右子树时会将右子树赋予cur促使右子树的入栈
        while (cur) {
            stack.push(cur);
            // 无论是否当前节点有左子树,都要将当前节点左子树赋值给游标
            // 这样才能造成cur为null,以跳出左子树遍历循环
            cur = cur.left;
        }
        if (stack[stack.length - 1].right) {
            // 因为外层循环条件是当前游标有值或栈不空
            // 所以可以将栈顶元素右子树赋予当前游标,然后将栈顶元素右子树致为null
            // 这里取了栈顶元素右子树,但没发生出栈行为
            cur = stack[stack.length - 1].right;
            // 因为一旦给 cur 手动赋值栈顶右子树后，下一次再走到这里的if判断时需要绕开(否则会进入这里)
            // 所以这里给if的判断条件致为null
            stack[stack.length - 1].right = null;
            // 这里相当于把树拆分开了,把右子树拆了出来
        } else {
            // 走到这里说明当前节点既没左子树
            // (上面的内层循环把所有左子树入栈,当没有左子树可入栈时才继续往下走)
            // 也没右子树
            // 这里是以"左右根"的根身份进入结果集的
            ans.push(stack.pop().val);
        }
    }
    return ans;
};
```

