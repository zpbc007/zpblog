---
title: 792. Number of Matching Subsequences
date: 2019-03-01 18:05:19
tags:
    - 算法
    - leetcode
keyword:
    -  匹配子序列的单词数
    -  Number of Matching Subsequences
    -  792
description:  匹配子序列的单词数，解题思路
---

## 题目描述

给定字符串 S 和单词字典 words, 求 words[i] 中是 S 的子序列的单词个数。

```
示例:
输入: 
S = "abcde"
words = ["a", "bb", "acd", "ace"]
输出: 3
解释: 有三个是 S 的子序列的单词: "a", "acd", "ace"。
```

#### 分析

首先遍历S记录S中各个字母出现的位置即:

```js
var  CharIndexMap = {
    a: [0],
    b: [1],
    c: [2],
    d: [3],
    e: [4],
}
```

接着遍历words中每个单词的字母，首先通过CharIndexMap找到该字母在S中出现的位置，如果words中的每个字母在CharIndexMap中都有对应的数组，且位置与当前字母顺序相同，则满足条件。

#### 代码

```js
/**
 * @param {string} S
 * @param {string[]} words
 * @return {number}
 */
var numMatchingSubseq = function(S, words) {
    const CharIndexMap = {};
    // 构建 字符-字符位置 索引
    for (let i = 0; i < S.length; i++) {
        if (CharIndexMap[S[i]]) {
            CharIndexMap[S[i]].push(i)
        } else {
            CharIndexMap[S[i]]=[i];
        }
    }

    // 存放word 匹配结果
    const WordResultMap = {}

    let result = 0;
    for (const word of words) {
        let matchResult = false;

        if (WordResultMap.hasOwnProperty(word)) {// 先取缓存结果
            matchResult = WordResultMap[word];
        } else {
            matchResult = isMatch(word, CharIndexMap)
        }

        if (matchResult) {
            result++;
        }

        WordResultMap[word] = matchResult;
    }

    return result;
};

/**
 * 查看word是否为子串
 * @param {string} word
 * @param {{[key: string]: number[]}} indexMap
 */
var isMatch = function (word, indexMap) {
    // 当前查找的位置
    let preIndex = -1;
    for (const char of word) {
        let curIndexArr = indexMap[char];

        // 字母不存在
        if (!curIndexArr) {
            return false;
        }

        // 找到下一个节点
        const index = lower_bound(curIndexArr, preIndex);
        if (index === -1) {// 没找到
            return false;
        } else {
            preIndex = curIndexArr[index];
        }
    }

    return true;
}

/**
 * 查找在arr中第一个比target大的数
 * @param {number[]} arr
 * @param {number} target
 */
var lower_bound = function(arr, target) {
    let len = arr.length,
        l = 0,
        h = len - 1;

    while(l < h) { // 二分查找
        const mid = Math.floor((h - l) / 2) + l;
        if (target < arr[mid]) {
            h = mid;
        } else {
            l = mid + 1;
        }
    }

    if (l < len && arr[l] > target) {
        return l;
    }

    return -1;
}
```