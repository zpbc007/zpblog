---
title: 树状数组(Binary Indexed Tree)
date: 2019-02-21 21:03:40
tags:
    - 算法
    - 数据结构
    - leetcode
    - Fenwick树
    - 二叉索引树
    - 树状数组
    - Binary Indexed Tree
---

## 简介

树状数组或二叉索引树(Binary Indexed Tree),因其发明者又叫Fenwick树。

此数据结构用于解决以下问题： 有长度为n的数组且每项的值不固定，现进行以下操作
        1. 向数组的第i项增加或减少值x(update 操作)
        2. 计算数组第p项到第q项的和(sumRange 操作)
        3. 重复以上步骤

### 原始解决方法

每次都遍历数组的第p到q项进行求和求和。

```ts
/**
 * 构造函数
 * @param {number[]} nums 初始给定数组
 */
const NumArray = function(nums) {
    this.nums = nums;
}

/** 
 * 更新操作
 * @param {number} i 更新第i项
 * @param {number} val 更新后的值
 * @return {void}
 */
NumArray.prototype.update = function(i, val) {
    this.nums[i] = val;
};

/**
 * 求和
 * @param {number} i 起始位置
 * @param {number} j 终止位置
 */
NumArray.prototype.sumRange = function(i, j) {
    const sum = 0;
    for (let index = i; index <= j; index++) {
        sum += this.nums[index];
    }

    return sum;
};
```

update操作的时间复杂度为O(1),sumRange操作的时间复杂度为O(n)。如果进行m次操作最坏的情况为O(m \* n)，而使用树状数组最坏的情况下时间复杂度为O(m \* log n)。

### 树状数组

#### 定义

该算法定义先将数组中的每个元素的index（从1开始）用其二进制进行表示，然后将其转为一棵树。在更新某一节点时，将该节点及其所有父级节点都加上变化的值，在查询时，将该节点及其所有父级节点的值相加即为该节点之前的所有节点的和。树的节点的定义如下：

- 更新时父级节点定义：某一节点的父节点为当前节点的二进制表示的最后一个1与自己相加对应的位置。如：
    位置为1的节点的二进制为0001，它的最后一个1为0001，相加后得到0010即位置为2。
    位置为2的节点的二进制为0010，它的最后一个1为0010，相加后得到0100即位置为4。
    位置为5的节点的二进制为0101，它的最后一个1为0001，相加后得到0110即位置为6。
    图示：![update](/images/binary_indexed_tree/update.jpg)
- 查询与更新相反，某一节点的父级节点为当前节点的二进制表示减去二进制表示的最后一个1，如：
    位置为8的节点的二进制为1000，它的最后一个1为1000， 相减后得到0000，小于1所以没有父节点。
    位置为7的节点的二进制为0111，它的最后一个1为0001，相减后得到0110即位置为6。
    位置为6的节点的二进制为0110，它的最后一个1为0010，相减后得到0100即位置为4。
    图示：![query](/images/binary_indexed_tree/query.jpg)

#### 实现

##### 1. 定义Lowbit函数，返回数字二进制表示的最后的1所代表的数字。

```ts
/**
 * @param {number} num 
 */
function lowbit(num) {
  return num & (-num);
}
```

##### 2. 实现更新操作

```ts
/**
* 更新节点
* @param index 节点位置
* @param delta 节点值的变化
*/
update(index: number, delta: number) {
    while (index < this.sumArr.length) {
        this.sumArr[index] += delta;
        index += this.lowbit(index);
    }
}
```
其中
```ts
index += this.lowbit(index);
```
即是节点不断向上查找父级节点的过程。

##### 3. 实现查询操作

```ts
/**
* 前index个节点值的和
* @param index 节点位置
*/
query(index: number) {
    let sum = 0;
    while(index > 0) {
        sum += this.sumArr[index];
        index -= this.lowbit(index);
    }

    return sum;
}
```

其中
```ts
index -= this.lowbit(index);
```

即是节点不断查找父级节点的过程

##### 4. 整体结构

```ts
class BinaryIndexedTree {
    private sumArr: number[] = [];
    constructor(size: number) {
        /** 数组从1开始 */
        this.sumArr = Array(size + 1).fill(0);
    }

    /**
     * 更新节点
     * @param index 节点位置
     * @param delta 节点值的变化
     */
    update(index: number, delta: number) {
        while (index < this.sumArr.length) {
            this.sumArr[index] += delta;
            index += this.lowbit(index);
        }
    }

    /**
     * 前index个节点值的和
     * @param index 节点位置
     */
    query(index: number) {
        let sum = 0;
        while(index > 0) {
            sum += this.sumArr[index];
            index -= this.lowbit(index);
        }

        return sum;
    }

    private lowbit(index: number) {
        return index & (-index);
    }
}
```