---
layout: post
title: "Container With Most Water"
date: 2020-01-05 11:35:06 +0800
comments: true
categories: leetcode
---

## 问题描述

假设有n个非负的整数a1,a2,...an，在二维坐标系中分别代表(1,a1),(2,a2)...(n,an)。分别以这些点为起点向x轴作垂线，以这些垂线为一遍x轴为一边找到最大的面积。

> n 至少为2

举例

```
Input: [1,8,6,2,5,4,8,3,7]
Output: 49
```

从数组的第2个数到最后一个数可以组成一个面积为7*7的矩型。

数组第2个数到第7个数组成的面积为8*6，小于49。

## 问题分析

比较简单的思路就是穷举，找到所有的组合的面积返回最大的即可，这样太挫了，不用这个思路了。

**以下是看了答案后的思考**

面积等于长乘以宽，尽可能的长和尽可能的宽能够获取到大的面积。长最大为n，宽最大为数组中的最大数。

首先有两个指针，分别指向第一个数和最后一个数，这个时候是最长的，但是面积不是最大的。长度的变化也是依赖数组中的数字大小，这个时候如何进行下一步是关键。根据尽可能长的标准，保留最长的一个边移动较短的一边的指针。计算出每次的面积，最后返回最大的面积。

代码如下

```c
int maxArea(int* height, int heightSize){
    int *first,*end,maxArea=0;
    int realHeight,tmpArea=0;
    first = &height[0];
    end = &height[heightSize-1];
    while (first <= end){
        realHeight = (*first)>=(*end)?(*end):(*first);
        tmpArea = realHeight * (end - first);
        if(tmpArea>maxArea){
            maxArea = tmpArea;
        }
        if(*first>=*end){
            end--;
        } else{
            first++;
        }
    }
    return maxArea;
}
```



运行结果：

```c
Runtime: 12 ms, faster than 94.13% of C online submissions for Container With Most Water.
Memory Usage: 7.8 MB, less than 42.86% of C online submissions for Container With Most Water.
```

