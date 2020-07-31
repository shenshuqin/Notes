#### [面试题57. 和为s的两个数字](https://leetcode-cn.com/problems/he-wei-sde-liang-ge-shu-zi-lcof/)

难度简单17

输入一个递增排序的数组和一个数字s，在数组中查找两个数，使得它们的和正好是s。如果有多对数字的和等于s，则输出任意一对即可。

 

**示例 1：**

```
输入：nums = [2,7,11,15], target = 9
输出：[2,7] 或者 [7,2]
```

### 解法----双指针

```js
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var twoSum = function(nums, target) {
  let left=0;
  let right=nums.length-1;
  while(left<right){
    if(nums[left]+nums[right]===target){
      return [nums[left],nums[right]];
    }else if(nums[left]+nums[right]<target){
      left++;
    }else{
      right--
    }
  }
};
```

