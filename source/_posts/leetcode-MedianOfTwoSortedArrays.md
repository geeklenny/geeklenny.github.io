---
title: leetcode-MedianOfTwoSortedArrays
comments: false
date: 2016-12-19 11:04:34
tags:
  - 程序设计
  - leetcode
categories:
  - 程序设计
  - leetcode
---

### [MedianOfTwoSortedArrays](https://leetcode.com/problems/median-of-two-sorted-arrays/)
There are two sorted arrays nums1 and nums2 of size m and n respectively.

Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).

<!--more-->

### example
> nums1 = [1, 3]
> nums2 = [2]
>
> The median is 2.0

> nums1 = [1, 2]
nums2 = [3, 4]

> The median is (2 + 3)/2 = 2.5


### 思路
　　本体对时间复杂度有要求。当我看到这个题目的时候，有两点引起我的注意，第一个就是两个数组都是有序的，第二个就是时间复杂度为log(n)。这是我想到了归并排序。就这样我按照归并排序的思路完成了本题。

部分细节：
1. 中位数并不总是一个。
2. 遍历数组不需要全部遍历。
3. 注意归并排序的代码实现。

### coding
```cpp
#include <iostream>
#include <vector>
#include <limits.h>
using namespace std;

class Solution {
  public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int size = nums1.size()+nums2.size();
        int mid1 = size/2;
        int mid2 = mid1 - (size%2==0?1:0);
        int temp, mid1num=0,mid2num=0,i=0,j=0;
        nums1.push_back(INT_MAX);
        nums2.push_back(INT_MAX);

        for (int k = 0 ; k < size; k++)
        {
            temp = 0;
            if( nums1[i] < nums2[j] )
            {
                temp = nums1[i];
                i++;
            }
            else
            {
                temp = nums2[j];
                j++;
            }
            if( k == mid2 )
            {
                mid2num = temp;
            }
            if( k == mid1 )
            {
                mid1num = temp;
                break;
            }
        }
        return (mid1num + mid2num) / 2.0;
    }
};

int main(int argc, char *argv[])
{
    Solution s;
    vector<int> nums1 = {1,3};
    vector<int> nums2 = {2};
    vector<int> nums3 = {1,2};
    vector<int> nums4 = {3,4};

    cout << s.findMedianSortedArrays(nums1, nums2) << endl;
    cout << s.findMedianSortedArrays(nums3, nums4) << endl;
    return 0;
}
```

### github

[github代码链接](https://github.com/geeklenny/leetcode)
