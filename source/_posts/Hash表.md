---
title: Hash表
date: 2026-06-23 16:37:42
tags: Hash表
excerpt: Hash表算法题目
categories:
    - 算法
---
# 找出和为目标值的那两个整数，并返回它们的数组下标。

本质加法的交换律

A+B=C  =>C-B=A

因为hash表有寄存（map）

给你一个整数数组 nums（比如 [2, 7, 11, 15]）和一个目标值 target（比如 比如 9）。

与其苦苦地往后寻找“另一个数”，不如看看前面有没有人刚好在等我

请你在该数组中找出和为目标值的那两个整数，并返回它们的数组下标。

笨方法拿2+7+.......,7+11+15+....时间复杂度On平方


```
function twoSum (num,target){
    const map = new Map();
    
    for (let i = 0; i< nums.length; i++ ){
        const currentNum = nums[i];
        const needNum = target - currentNum;

        if (map.has(needNum)){
            return [map.get(neeedNum),i];
        }
        map.set(currentNum,i);
    }
}

```

# 