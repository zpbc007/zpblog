---
title: 848. Shifting Letters
date: 2019-02-28 17:46:57
tags:
    - 算法
    - leetcode
keyword: 
    - Shifting Letters
    - 字母移位
    - 848
description: Shifting Letters 解题思路
---

## 题目描述

[848. Shifting Letters](https://leetcode-cn.com/problems/shifting-letters/)给定一个由小写字符串组成的字符串**S**和一个整数数组**shifts**。定义将字母转换为它的下一个字母的操作为移位操作。
例如：shift('a') = 'b', shift('b') = 'c', shift('z') = 'a'。
我们会将shifts数组中的第i位对应的S[i]到S[0]执行shifts[i]次shift操作。

#### 分析

S中的第i个字母需要执行shift操作的次数为shifts[i] + shifts[shifts.length -1]即从当前位置到最末位的和。
因此从后向前遍历shifts数组，并记录从后向前的和，然后将对应的字母移位即可。

#### 代码

```js
/**
 * @param {string} S
 * @param {number[]} shifts
 * @return {string}
 */
var shiftingLetters = function(S, shifts) {
    let sum = 0;
    // 最后的结果
    let result = '';
    // 从后向前遍历数组
    for (let i = shifts.length - 1; i >=0; i--) {
        // 当前字母需要移位的次数
        sum = shifts[i] + sum;
        // 因为是由后向前遍历
        result = shiftChar(S[i], sum) + result;
    }

    return result;
};

/**
 * 字母转换
 * @param {string} char 
 * @param {number} num 
 */
function shiftChar(char, num) {
    let finalCode = char.charCodeAt(0) + num % 26;
    if (finalCode > 122) {
        finalCode = finalCode - 26;
    }

    return String.fromCharCode(finalCode);
}
```