---
layout: post
title: "Add Two Numbers"
date: 2019-04-08 22:43:39 +0800
comments: true
categories: leetcode
---

### 我的代码

#### 代码一

```c
void add(int *value,int *step){
    if(*value>=10){
        *step=*value/10;
        *value%=10;
    }else
    {
        *step=0;
    }
    
}

struct ListNode* addTwoNumbers(struct ListNode* l1, struct ListNode* l2) {
    int step=0,tmpValue;
    struct ListNode *result=NULL;
    struct ListNode *current=NULL;
    struct ListNode *pre=NULL;
    for(;l1!=NULL&&l2!=NULL;l1=l1->next,l2=l2->next){
        tmpValue=l1->val+l2->val+step;
        add(&tmpValue,&step);
        current=(struct ListNode*)malloc(sizeof(struct ListNode));
       
        current->val=tmpValue;
        if(!pre&&!result){
            result=current;
            pre=current;
        }else
        {
            pre->next=current;
            pre=current;
        }
    }
    if(l1){
        for(;l1;l1=l1->next){
            current=(struct ListNode*)malloc(sizeof(struct ListNode));
            tmpValue=l1->val+step;
            add(&tmpValue,&step);
            current->val=tmpValue;
            pre->next=current;
            pre=current;
        }
    }
    if(l2){
        for(;l2;l2=l2->next){
            current=(struct ListNode*)malloc(sizeof(struct ListNode));
            tmpValue=l2->val+step;
            add(&tmpValue,&step);
            current->val=tmpValue;
            pre->next=current;
            pre=current;
        }
    }
    if(step>0){
        current=(struct ListNode*)malloc(sizeof(struct ListNode));
        current->val=step;
        pre->next=current;
        pre=current;
    }
    current->next=NULL;
    return result;
}
```

#### 运行结果

```
Runtime: 32 ms
Memory Usage: 17.9 MB
```

