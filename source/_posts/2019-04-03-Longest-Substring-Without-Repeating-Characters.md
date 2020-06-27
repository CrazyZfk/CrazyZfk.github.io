---
layout: post
title:  " Longest Substring Without Repeating Characters"
date:   2019-04-03 20:00:00 +0800
categories: leetcode
---
### 我的代码

#### 代码一

```c
int lengthOfLongestSubstring(char* s) {
    char *sp;
    int array[128]={0};
    int i,j,len,max;
    sp=s;
    len=0;
    max=0;
    while(*sp){
        i=*sp;
        if(array[i]==0){
            len++;
            array[i]=len;
            sp++;
        }else{
            sp-=len-array[i];
            if(len>max){
                max=len;
            }
            len=0;
            for(j=0;j<128;j++){
                array[j]=0;
            }
        } 
    }
    if(len>max){
        max=len;
    }
    return max;
    
}
```

### 运行结果

```
Runtime: 28 ms, faster than 38.82% of C online submissions for Longest Substring Without Repeating Characters.
Memory Usage: 7.1 MB, less than 100.00% of C online submissions for Longest Substring Without Repeating Characters
```

