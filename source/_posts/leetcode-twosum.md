---
title: leetcode-two sum
comments: false
date: 2016-12-09 15:56:03
tags:
  - 程序设计
  - leetcode
categories:
  - 程序设计
  - leetcode
---

### [twoSum](https://leetcode.com/problems/two-sum/)
Given an array of integers, return indices of the two numbers such that they add up to a specific target.

You may assume that each input would have exactly one solution.

<!--more-->

### example
> Given nums = [2, 7, 11, 15], target = 9,

> Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].

### 思路
　　本题思路并不难。可以从第一个数开始遍历，与后面的所有剩余的数字进行加法运算，如果符合预期则返回。否则继续遍历。这里有一个很明显的问题就是增加了很多不必要的运算。算法时间复杂度较高。
　　一个很巧妙的算法是通过map存储已经遍历过的数字。key存储其值，value存储其索引。通过对数字从头到尾遍历。计算目标值(target-当前值)，并到map中查找，如果存在则返回，若不存在，将当前值存储到map中继续遍历。这样只需遍历一边数组就可完成整个算法。

### coding
```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;

class Solution {
  public:
    vector<int> twoSum(vector<int>& nums, int target)
    {
        unordered_map<int,int> record;
        vector<int> result;
        for( int i = 0; i < nums.size(); i++)
        {
            int numberToFind = target - nums[i];
            if(record.find(numberToFind) != record.end())
            {
                result.push_back(record[numberToFind]);
                result.push_back(i);
                return result;
            }
            record[nums[i]]=i;
        }
        return result;
    }
};

int main(int argc, char *argv[])
{
    Solution s;
    vector<int> test = {2,7,11,15};
    int target = 9;

    for(int i : s.twoSum(test, target))
        cout << i << endl;

    return 0;
}
```

### github

[github代码链接](https://github.com/geeklenny/leetcode)
