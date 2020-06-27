---
layout: post
title: "ZigZag Conversion"
date: 2019-12-21 12:08:10 +0800
comments: true
categories: leetcode
---

## 问题描述

在给定行数的情况下字符串 "PAYPALISHIRING" 可以被写成如下的之字型：

```c
P   A   H   N
A P L S I I G
Y   I   R
```

按照行读取的字符串为："PAHNAPLSIIGYIR"

写代码指定行数完成上面的转换

```c
string convert(string s, int numRows);
```

### 例子1

```c
Input: s = "PAYPALISHIRING", numRows = 3
Output: "PAHNAPLSIIGYIR"
```

### 例子2

```c
Input: s = "PAYPALISHIRING", numRows = 4
Output: "PINALSIGYAHRPI"
Explanation:

P     I    N
A   L S  I G
Y A   H R
P     I
```

## 问题分析

我们按列来分析下，没一列的长度都是指定的row大小(除去最后一列)。这表示从这个列的开始到下个列的开始也是相隔了 2row-2 的长度。再从每一列单独来看如果这列的后面还有一列则随着行数的增加排在同行的字符间隔减少2。

以上面例子2中的第一列为例：

```
P的后面是I 中间间隔2row-2=6，A的后面是L间隔4，Y的后面是A间隔2，P的后面间隔0代表没有。
```

### 代码实现

```
char * convert(char * s, int numRows){
    int sLen=0,index=0,pos=0,nextPos=0,col=0,nextCol=0,colSkip = 2*numRows-2;
    sLen = strlen(s);
    char *result = (char *)malloc(sizeof(char)*(sLen+1));
    if(!colSkip){
        return s;
    }
    for(index=0;index<numRows;index++){
        col=0;
        nextPos=0;
        for(nextCol=index;nextPos<sLen;nextCol=colSkip*(++col)+index){
            nextPos = nextCol-2*index+colSkip;
            if(nextCol<sLen){
                result[pos++] = s[nextCol];
            }
            if(nextPos>nextCol&&nextPos<nextCol+colSkip&&nextPos<sLen){
                result[pos++] = s[nextPos];
            }
        }
    }
    result[pos]='\0';
    return result;

}
```

### 运行结果

```c
Runtime: 4 ms, faster than 88.04% of C online submissions for ZigZag Conversion.
Memory Usage: 7.8 MB, less than 100.00% of C online submissions for ZigZag Conversion.
```

### 其他思路

一行用一个字符数组表示，字符数组写入数据的顺序由是否第一行和最后一行来控制。**具体代码见leetcode关于本题的讨论**

## 后记

### corner case

```c
"A" 1
"ABC" 1
```