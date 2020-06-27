---
layout: post
title: "String to Integer (atoi)"
date: 2020-01-01 17:31:34 +0800
comments: true
categories: leetcode
---

## 问题描述

实现atoi函数将string类型转换为int。

注意

1. 只有' '被认为是空字符
2. 只处理32位整数范围 [-2^31,2^31-1],超过这个范围的整数则返回对应的最大值和最小值。

举例

```c
Input: "42"
Output: 42
```

```c
Input: "   -42"
Output: -42
```

```c
Input: "4193 with words"
Output: 4193
```

```c
Input: "words and 987"
Output: 0
```

```c
Input: "-91283472332"
Output: -2147483648
```

## 问题分析

首先考虑特殊情况

1. 全是空格的字符串
2. 第一个非空的字符串不是数字也不是+/-符号的字符串

将上面两种情况首先排除，上面的情况可以直接返回0。

```c
          for(index=0;index<size;index++){
        if(str[index]==' '){
            if(beg==-1) {
                continue;
            }else{
                break;
            }
        }
        if(str[index]>='0'&&str[index]<='9'&&beg==-1){
            beg = index;
            end = index;
            continue;
        }
        if((str[index]=='+'||str[index]=='-')&&beg==-1){
            beg = index;
            end = index;
            continue;
        }
        if(str[index]<'0'||str[index]>'9'){
            if(beg==-1){
                return 0;
            } else{
                end = index-1;
                break;
            }
        }
        end = index;
    }
    if(beg<0||(beg==end)&&(str[beg]=='+'||str[beg]=='-')){
        return 0;
    }
```

剩下的部分就是提取出需要的字符串,根据与最大和最小整数字符串比较来判断是否超过了范围。

```
char* MAX_INT_WITHOUTSIGN = "2147483647";
char* MIN_INT_WITHOUTSIGN = "2147483648";
int MAX_LENGTH = 10;
```



```c
   if(str[beg]=='-'||str[beg]=='+'){
        realBeg = beg+1;
    } else{
        realBeg = beg;
    }
    while(str[realBeg]=='0'){
        realBeg++;
    }
    numbers = end - realBeg+1;
    if(numbers>MAX_LENGTH){
        if(str[beg]=='-'){
            return -2147483648;
        }else{
            return 2147483647;
        }
    }
    point = (char*)malloc(sizeof(char)*(numbers+1));
    for(index=realBeg;index<=end;index++){
        point[pos++]=str[index];
    }
    point[pos]='\0';
    if(numbers>=MAX_LENGTH){
        if(str[beg]=='-'){
            if(strcmp(point,MIN_INT_WITHOUTSIGN)>=0){
                return -2147483648;
            }
        }else {
            if (strcmp(point, MAX_INT_WITHOUTSIGN) >= 0) {
                return 2147483647;
            }
        }
    }
```

剩下的部分就比较简单了，直接按照位数乘以10项加起来就可以了

```c
    for(index=numbers-1,pos=0;index>=0;index--,pos++){
        charToNumber=point[index]-'0';
        for(tenTimePos=0;tenTimePos<pos;tenTimePos++){
            charToNumber*=10;
        }
        result+=charToNumber;
    }
    free(point);
    if(str[beg]=='-'){
        return 0-result;
    }
    return result;
```

完整的代码如下：

```c
char* MAX_INT_WITHOUTSIGN = "2147483647";
char* MIN_INT_WITHOUTSIGN = "2147483648";
int MAX_LENGTH = 10;
int myAtoi(char * str){
    int size = 0,realBeg = -1,beg=-1,end=-1,index=0,numbers=0,pos=0,result=0;
    int charToNumber,tenTimePos;
    char *point;
    size = strlen(str);
    if(size==0){
        return 0;
    }
    for(index=0;index<size;index++){
        if(str[index]==' '){
            if(beg==-1) {
                continue;
            }else{
                break;
            }
        }
        if(str[index]>='0'&&str[index]<='9'&&beg==-1){
            beg = index;
            end = index;
            continue;
        }
        if((str[index]=='+'||str[index]=='-')&&beg==-1){
            beg = index;
            end = index;
            continue;
        }
        if(str[index]<'0'||str[index]>'9'){
            if(beg==-1){
                return 0;
            } else{
                end = index-1;
                break;
            }
        }
        end = index;
    }
    if(beg<0||(beg==end)&&(str[beg]=='+'||str[beg]=='-')){
        return 0;
    }
    if(str[beg]=='-'||str[beg]=='+'){
        realBeg = beg+1;
    } else{
        realBeg = beg;
    }
    while(str[realBeg]=='0'){
        realBeg++;
    }
    numbers = end - realBeg+1;
    if(numbers>MAX_LENGTH){
        if(str[beg]=='-'){
            return -2147483648;
        }else{
            return 2147483647;
        }
    }
    point = (char*)malloc(sizeof(char)*(numbers+1));
    for(index=realBeg;index<=end;index++){
        point[pos++]=str[index];
    }
    point[pos]='\0';
    if(numbers>=MAX_LENGTH){
        if(str[beg]=='-'){
            if(strcmp(point,MIN_INT_WITHOUTSIGN)>=0){
                return -2147483648;
            }
        }else {
            if (strcmp(point, MAX_INT_WITHOUTSIGN) >= 0) {
                return 2147483647;
            }
        }
    }
    for(index=numbers-1,pos=0;index>=0;index--,pos++){
        charToNumber=point[index]-'0';
        for(tenTimePos=0;tenTimePos<pos;tenTimePos++){
            charToNumber*=10;
        }
        result+=charToNumber;
    }
    free(point);
    if(str[beg]=='-'){
        return 0-result;
    }
    return result;

}
```

运行结果(有点懵，同样的代码得到不同的结果)

```c
Runtime: 4 ms, faster than 66.31% of C online submissions for String to Integer (atoi).
Memory Usage: 6.9 MB, less than 71.43% of C online submissions for String to Integer (atoi).
```

```
Runtime: 0 ms, faster than 100.00% of C online submissions for String to Integer (atoi).
Memory Usage: 7.1 MB, less than 14.29% of C online submissions for String to Integer (atoi).
```

