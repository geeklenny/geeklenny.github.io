---
title: leetcode-LongestPalindromicSubstring
comments: false
date: 2016-12-20 14:30:01
tags:
  - 程序设计
  - leetcode
tegories:
  - 程序设计
  - leetcode
---


### [LongestPalidrmicSubstring](https://leetcode.com/problems/longest-palindromic-substring/)
Given a string s, find the longest palindromic substring in s. You may assume that the maximum length of s is 1000.
<!--more-->

### example
> Input: "babad"

> Output: "bab"

> Note: "aba" is also a valid answer.

> Input: "cbbd"

> Output: "bb"


### 思路
　　这个题我的答案并不巧妙，只能说是完成了任务。不过还是要说一下注意点:
1. 对连续的相同字符的处理
2. 如果a[p..q]是palidrmic串，如果a[p-1]=a[q+1]，那么a[p-1..q+1]也是palidrmic串

　　在leetcode的discuss专栏里有很多出色的解答，希望大家移步讨论区去自行学习。

### coding
```cpp
#include <iostream>
#include <string>

using namespace std;

class Solution {
  public:

    void GetInitSbutString(string s, int n, int &start, int& end)
    {
        while(start - 1 >= 0)
        {
            if( s.at(start - 1 ) == s.at(n) )
                start = start -1;
            else break;
        }
        while(end + 1 < s.length())
        {
            if( s.at(end+1) == s.at(n) )
                end = end+1;
            else break;
        }
    }

    int getSubstring(string s, int& start)
    {
        int tempStart = start, tempEnd = start;

        GetInitSbutString(s, start, tempStart, tempEnd);
        while( (tempStart - 1 >= 0 ) && (tempEnd + 1 < s.length() ) )
        {
            if( s.at(tempStart-1) == s.at(tempEnd+1))
            {
                tempStart = tempStart - 1;
                tempEnd = tempEnd + 1;
            }
            else break;
        }

        start = tempStart;
        return tempEnd-tempStart +1;
    }

    string longestPalindrome(string s) {
        int start = 0,length=0;
        for(int i =0; i < s.length(); i++)
        {
            int tempStart = i;
            int l = getSubstring(s,tempStart);
            if (length <= l)
            {
                length = l;
                start = tempStart;
            }
        }
        return s.substr(start, length);
    }
};

int main(int argc, char *argv[])
{
    Solution s;
    string temp = "babad";
    cout << s.longestPalindrome(temp) << endl;
    temp = "cbbd";
    cout << s.longestPalindrome(temp) << endl;
    temp = "aba";
    cout << s.longestPalindrome(temp) << endl;
    return 0;
}
```

### github

[github代码链接](https://github.com/geeklenny/leetcode)
