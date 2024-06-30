---
title: JavaScript经典算法题
date: 2023/11/01 20:32
updated: 2023/11/01 20:32
categories:
- [技术, 数据结构和算法]
tags:
- 数据结构和算法
- JavaScript
---

## 两数之和

给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出和为目标值 target 的那**两个**整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。



### 题解

```js
function twoSum(nums, target) {
  for (let i = 0; i < nums.length; i++) {
    for (let j = i + 1; j < nums.length; j++) {
      if (nums[i] + nums[j] == target) {
        return [i, j]
      }
    }
  }
}

module.exports = twoSum;
```

## 二维数组中查找数字

在一个 `n * m` 的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个高效的函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

示例：

现有矩阵 matrix 如下：

```js
[
  [1, 4, 7, 11, 15],
  [2, 5, 8, 12, 19],
  [3, 6, 9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30],
];
```

给定 `target = 5`，返回 true。 给定 `target = 20`，返回 false。



### 题解

```js
function findNumberIn2DArray(matrix, target) {
  for (let i = 0; i < matrix.length; i++) {
    if (matrix[i].indexOf(target) !== -1) {
      return true;
    }
  }
  return false;
}

module.exports = findNumberIn2DArray;

```


## 找出数组中重复的数字

新建一个 `findRepeatNumber.js` 文件，在文件里写一个名为 `findRepeatNumber` 的函数。找出数组中重复的数字。

```js
function findRepeatNumber(nums) {
  // 补充代码
}

module.exports = findRepeatNumber;
```



### 题解

```js
function findRepeatNumber(nums) {
    const set = new Set(nums)
    return nums.find(x => !set.delete(x))
}

module.exports = findRepeatNumber
```

## 替换空格

请实现一个函数，把字符串 `s` 中的每个空格替换成"%20"。

### 题解

```js
function replaceSpace(s) {
  return s.split(" ").join("%20");
}

module.exports = replaceSpace;

```

(完)







**在线题库**

- [两数之和](https://www.lanqiao.cn/problems/2495/learning/)
- [二维数组中查找数字](https://www.lanqiao.cn/problems/2497/learning)
- [数组中重复的数字 ](https://www.lanqiao.cn/problems/2494/learning/)
- [替换空格](https://www.lanqiao.cn/problems/2494/learning/)
