---
title: leetcode-AddTwoNum
comments: false
date: 2016-12-12 17:18:20
tags:
  - 程序设计
  - leetcode
categories:
  - 程序设计
  - leetcode
---


### [AddTwoNum](https://leetcode.com/problems/add-two-numbers/)
You are given two linked lists representing two non-negative numbers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

<!--more-->

### example
> Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
> Output: 7 -> 0 -> 8

### 思路
　　思路不算复杂，就是按照我们正常的加法竖式计算的方式来完成此题就可以。需要注意的细节有两点:
1. 进位值
2. 判断list结尾

　　关于进位值(程序中定义为extra变量)在本题中，如果是list最后两个数字相加的进位，往往会被忽略。第二点，两个list不一定是等长的，所以在遍历的过程中，需要对list结尾进行判断，防止运行时空指针错误。注意这两点，可能就可以正确完成本题了。

### coding
```cpp
#include <iostream>

using namespace std;


struct ListNode {
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(NULL) {}
};


class Solution {
  public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode result(0);
        ListNode* tempResult = &result;
        int l1Value, l2Value, extra;
        l1Value = l2Value = extra = 0;
        while(l1 != NULL || l2 != NULL || extra != 0)
        {
            l1Value = (l1!=NULL)?l1->val : 0;
            l2Value = (l2!=NULL)?l2->val : 0;
            tempResult->next = new ListNode((l1Value+l2Value+extra)%10);
            extra = (l1Value+l2Value+extra) / 10;

            l1 != NULL ? l1 = l1->next : l1 = NULL;
            l2 != NULL ? l2 = l2->next : l2 = NULL;
            tempResult = tempResult->next;
        }
        return result.next;
    }
};

int main(int argc, char *argv[])
{
    ListNode* l1 = new ListNode(2);
    l1->next = new ListNode(4);
    l1->next->next = new ListNode(3);

    ListNode* l2 = new ListNode(5);
    l2->next = new ListNode(6);
    l2->next->next = new ListNode(4);
    //l2->next->next = new ListNode(7);

    Solution s;
    ListNode* result = s.addTwoNumbers(l1,l2);

    while(result)
    {
        cout << result->val << endl;
        result = result->next;
    }
    return 0;
}
```

### github

[github代码链接](https://github.com/geeklenny/leetcode)
