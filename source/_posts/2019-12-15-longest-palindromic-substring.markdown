---
layout: post
title: "Longest Palindromic Substring"
date: 2019-12-15 12:50:37 +0800
comments: true
categories: leetcode
---

## 问题描述

找出字符串s中最长的回文子串，假设s最长为1000.

例子1：

```c
Input: "babad"
Output: "bab"
Note: "aba" is also a valid answer.
```

例子2:

```c
Input: "cbbd"
Output: "bb"
```

## 问题分析

1. 首先需要找到字符串中的所有回文子串。
2. 选择上一步中所有回文子串中最长的。

### 查找回文子串

回文字符串的最大特点是整个字符串是对称的结构，可以使用两个指针来分别代表子串的开始和结束。然后在确定子串是否是对称的，由此来判断字符串是否是回文的。

下面的代码是打印所有的回文子串并返回最长子串的长度：

```c
int findPalindromicString(char *s){
    char *sp=s,*ep=s+1;
    char *tmpSp,*tmpEp;
    bool isPalindromic=true;
    int result =0;
    int tmpResult =0;
    while (*sp)
    {
        while(*ep){
            for(tmpSp=sp,tmpEp=ep;tmpSp<tmpEp;tmpSp++,tmpEp--){
                if(*tmpSp != *tmpEp){
                    isPalindromic = false;
                    break;
                }
            }
            if(isPalindromic){
                for(tmpSp = sp;tmpSp<=ep;tmpSp++){
                    tmpResult++;
                    printf("%c",*tmpSp);
                }
                printf("\n");
                if(tmpResult > result){
                    result = tmpResult;
                }
            }
            isPalindromic = true;
            ep++;
        }
        sp++;
        ep = sp + 1;
        isPalindromic = true;
        tmpResult = 0;
    }
    return result;    
}
```

### 查找最长回文子串

有了上面的例子返回最长子串就比较简单了，只要有个char *保存最长的子串然后返回就可以了。

```c
char* findPalindromicString(char *s){
    char *sp=s,*ep=s+1;
    char *tmpSp,*tmpEp;
    char *startPoint=s;
    bool isPalindromic=true;
    int max =1;
    int tmpLength =0;
    char *result;
    int index;
    if(!(*sp)){
        return "";
    }
    while (*sp)
    {
        while(*ep){
            for(tmpSp=sp,tmpEp=ep;tmpSp<tmpEp;tmpSp++,tmpEp--){
                if(*tmpSp != *tmpEp){
                    isPalindromic = false;
                    break;
                }
            }
            if(isPalindromic){
                tmpLength = ep - sp + 1;
                if(tmpLength > max){
                    max = tmpLength;
                    startPoint = sp;
                }
            }
            isPalindromic = true;
            ep++;
        }
        sp++;
        ep = sp + 1;
        isPalindromic = true;
        tmpLength = 0;
    }
    result = (char*)malloc(sizeof(char)*(max+1));
    for(index=0;index<max;index++){
        result[index]=*(startPoint++); 
    }
    result[max]='\0';
    return result;    
}
```

## 运行结果

下面为在leetcode的运行结果

```c
Runtime: 1644 ms, faster than 5.05% of C online submissions for Longest Palindromic Substring.
Memory Usage: 7.3 MB, less than 18.18% of C online submissions for Longest Palindromic Substring.
```

**效果很差**

## 代码优化

### 时间复杂度优化

从上面的代码中可以看到查找回文字符串的时间复杂度至少为O(n^2),这个时间主要花在了字符串的遍历上面。一共有这么多的遍历：

```c
n(n-1)+(n-1)(n-2)+...+2*1
```

为了找到所有可能的回文子串，遍历了所有的子串，通过检查子串来确定是否是回文的。

思考：是否可以不检查所有的字符串就能找到回文字符串？

回文字符串是对称的，对称的一个重要特点是从中间开始两边的字符串相等。如果按照这个思路来解决的话我们就可以很快的判断一个子串是不是回文的，而且不用检查所有的字符串。

举个例子：

```c
查找abcbd字符串的最长回文
```

按照以前的思路我们需要检查的子串如下：

```c
ab
abc
abcb
abcbd
bc
bcb //回文子串
bcbd
cb
cbd
bd
```

如果按照新的思路来考虑需要检查下面的子串：

```c
abc
bcb//回文子串
cbd
```

从两个思路需要检查的子串数量可以看出优化后方法的时间复杂度会下降很多。

新思路代码：

```c
char * findPalindromicStringOpti1(char *s){
    char *sp = s,*lower,*upper,*startPoint=s;
    int maxLength=1,tmpLength=1,index=0;
    char * result;
    bool odd=false;
    if(!(*s)||!(*(s+1))){
        return s;
    }
    if(!*(sp+2)){
        if(*(sp)==*(sp+1)){
            maxLength = 2;
            startPoint = s;
        }
    }else{
        for(sp=sp+1;*(sp+1);sp++){
            lower = sp - 1;
            upper = sp + 1;
            for(;lower>=s&&*upper&&*lower==*upper;lower--,upper++);
            tmpLength = upper - lower - 1;
            if(tmpLength > maxLength){
                maxLength = tmpLength;
                startPoint = ++lower;
            }
            lower = sp;
            upper = sp+1;
            if(*(sp-1)==*sp){
                lower = sp-1;
                upper =sp;
            }
            if(*(sp+1)==*sp){
                upper = sp+1;
                lower = sp;
            }
            for(;lower>=s&&*upper&&*lower==*upper;lower--,upper++);
            tmpLength = upper - lower - 1;
            if(tmpLength > maxLength){
                maxLength = tmpLength;
                startPoint = ++lower;
            }
        }
    }
    result = (char*)malloc(sizeof(char)*(maxLength+1));
    for(index=0;index<maxLength;index++){
        result[index]=*(startPoint++); 
    }
    result[maxLength]='\0';
    return result; 

}
```

优化后的运行结果

```c
Runtime: 12 ms, faster than 86.53% of C online submissions for Longest Palindromic Substring.
Memory Usage: 7.1 MB, less than 90.91% of C online submissions for Longest Palindromic Substring.
```

**优化后无论是时间还是内存都有了很大的提升**

有上可以看出来时间和空间复杂度在换个思路解题后都有了很大的提升。时间和空间的矛盾只有在其中一方已经优化到很好的时候才会体现。

### 空间复杂度优化

同上

### 别人的思路

#### 动态规划

字符串的长度为N

i，j均小于N

P(i,j)为下标从i到j的子串

如果P(i,j)为回文子串，则P(i+1,j-1)也是回文的。

反向可以得出如果一个子串是回文的那么它的子串也一定是回文的，即：

```c
P(i,j) = P(i+1,j-1)&&S[i]==S[j]
```

实现代码如下：

```c
char * findPalindromicStringOptiDp(char *s){
    int sLength = strlen(s);
    int **store,index=0,maxLength=1,i=0,j=0;
    char *startPoint=s,*result;
    if(!(*s)||!(*(s+1))){
        return s;
    }
    store = (int **)malloc(sizeof(int *)*sLength);
    for(index=0;index<sLength;index++){
        store[index] = (int*)malloc(sizeof(int)*sLength);
        for(i=0;i<sLength;i++){
            store[index][i] = 0;
        }
    }
    for(i=0;i<sLength;i++){
        for(j=0;j<=i;j++){
            if(i-j<2){
                store[j][i] = s[j]==s[i]?1:0;
            }else{
                store[j][i]= (store[j+1][i-1]&&(s[j]==s[i]))?1:0;
            }
            if(store[j][i]&&(i-j+1)>maxLength){
                startPoint = s+j;
                maxLength = i-j+1;
            }
        }
    }
    result = (char*)malloc(sizeof(char)*(maxLength+1));
    for(index=0;index<maxLength;index++){
        result[index]=*(startPoint++); 
    }
    result[maxLength]='\0';
    free(store);
    return result; 
}
```

运行结果如下：

```c
Runtime: 224 ms, faster than 13.32% of C online submissions for Longest Palindromic Substring.
Memory Usage: 183.1 MB, less than 9.09% of C online submissions for Longest Palindromic Substring.
```

由运行结果可以看到，代码使用了大量的空间运行来保存所有子串是否是回文的状态(此处可以优化，比如使用位图来表示)。运行时间也要比简单粗暴检索字符串的方式短很多。

## 后记

### 需要考虑的corner case

```c
""
"a"
"bbb"
"bb"
"ccb"
"ac"
```

