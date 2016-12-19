---
title: leetcode-longestSubStringWithOutRepeatingCharacters
comments: false
date: 2016-12-13 15:20:24
tags:
  - 程序设计
  - leetcode
categories:
  - 程序设计
  - leetcode
---

### [longest-substring-without-repeating-characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
Given a string, find the length of the longest substring without repeating characters.

<!--more-->

### example
> Given "abcabcbb", the answer is "abc", which the length is 3.

> Given "bbbbb", the answer is "b", with the length of 1.

> Given "pwwkew", the answer is "wke", with the length of 3. Note that the answer must be a substring, "pwke" is a subsequence and not a substring.

### 思路
　　阅读本题后，都会有大致的概念应该怎么去处理。大题思路就是从头到位遍历字符串，记录所有的没有出现重复字符的子字符串，并记录其长度。最后选出最大长度输出。这个思路理论上应该可以解决我们的问题，但是如何写出漂亮解答，还是需要我们稍作思考的。
　　让我们再重新审阅一下这个题目。如果我们假设子字符串s[i..j]为无重复子字符串，其长度为ｌ。如果s[j+1]也不存在在s[i..j]中，那么s[i..j+1]也是无重复子字符串，其长度更新为l+1。根据这种设计，我们完全可以套用动态规划来解决这个问题。（前面的文章有介绍)
　　那么我们进而去丰富我们的动态规划设计：
1. 标记子字符串起点substringstart。若s[j+1]与s[i..j]中无重复字符，那么s[i..j+1]的子字符串起点也为i；如果与r(i≤r≤j)重复，则其起点变为r+1;
2. 记录最大长度longest。当前长度[i..j]为j-i+1，与longest做比较，则其较大的更新longest。
3. 因为使用hashmap来优化记录存储。

### coding
```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
  public:
    int lengthOfLongestSubstring(string s) {
        vector<int> hashMap(256, -1);
        int longest = 0, subStringStart = 0;
        for(int i = 0; i < s.length(); i++)
        {
            subStringStart = subStringStart >= hashMap[s[i]] + 1 ? subStringStart : hashMap[s[i]] + 1;
            hashMap[s[i]] = i;
            longest = longest >= i-subStringStart +1 ? longest : i - subStringStart + 1;
        }
        return longest;
    }

};

int main(int argc, char *argv[])
{
    string s1 = "abcabcbb";
    string s2 = "a";
    string s3 = "abc";
    string s4 = "aab";
    Solution solution;
    cout << solution.lengthOfLongestSubstring(s1) << endl;
    cout << solution.lengthOfLongestSubstring(s2) << endl;
    cout << solution.lengthOfLongestSubstring(s3) << endl;
    cout << solution.lengthOfLongestSubstring(s4) << endl;
    return 0;
}
```

### github

[github代码链接](https://github.com/geeklenny/leetcode)
