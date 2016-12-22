---
title: leetcode-ReverseInteger
comments: false
date: 2016-12-21 15:13:37
tags:
  - 程序设计
  - leetcode
categories:
  - 程序设计
  - leetcode
---

### [Reverse Integer](https://leetcode.com/problems/reverse-integer/)
Reverse digits of an integer.
> Example1: x = 123, return 321
>
> Example2: x = -123, return -321

<!--more-->

### 思路
　　本题其实思路很简单，我个人认为只是问题的考察点，应该是对人们对边界的认识。简单的翻转我们都能想到，通过按10取余来获取最后一位,通过除以10来进行移位。一定要记得考虑int的边界。

### coding
```cpp
#include<iostream>
#include "limits.h"

using namespace std;

class Solution {
  public:
    int reverse(int x) {
        long long result = 0;
        while(x != 0)
        {
            result = result * 10 + x % 10;
            x = x/10;
        }
        return (result > INT_MAX || result < INT_MIN) ? 0 : result ;
    }
};

int main(int argc, char *argv[])
{
    Solution s;
    cout << s.reverse(123456) << endl;
    cout << s.reverse(1000) << endl;
    cout << s.reverse(1001) << endl;
    cout << s.reverse(0) << endl;
    cout << s.reverse(-123) << endl;
    cout << s.reverse(-1234) << endl;
    cout << s.reverse(1534236469) << endl;
    cout << 9646324350+1 << endl;

    return 0;
}
```

### github

[github代码链接](https://github.com/geeklenny/leetcode)
