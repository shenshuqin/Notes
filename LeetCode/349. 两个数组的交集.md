#### [349. 两个数组的交集](https://leetcode-cn.com/problems/intersection-of-two-arrays/)

难度简单174

给定两个数组，编写一个函数来计算它们的交集。

**示例 1:**

```
输入: nums1 = [1,2,2,1], nums2 = [2,2]
输出: [2]
```

**示例 2:**

```
输入: nums1 = [4,9,5], nums2 = [9,4,9,8,4]
输出: [9,4]
```

### 解法

解法1:

```js
/**
 * @param {number[]} nums1
 * @param {number[]} nums2
 * @return {number[]}
 */
var intersection = function(nums1, nums2) {
  let arr = [] ; 
  if(nums1.length > nums2.length)
   [nums1,nums2] = [nums2,nums1];
        for(let j =0;j<nums2.length;j++){
            if(nums1.indexOf(nums2[j])>-1){
                arr.push(nums2[j])
            }
        }
    return [...new Set(arr)]
};
```

解法2:使用fitler过滤,去重

```js
/**
 * @param {number[]} nums1
 * @param {number[]} nums2
 * @return {number[]}
 */
var intersection = function(nums1, nums2) {
    return [...new Set(nums1.filter((item)=>nums2.includes(item)))]
};

```

