### 搜索插入位置

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

你可以假设数组中无重复元素。

**示例 1:**

```
输入: [1,3,5,6], 5
输出: 2
```

**示例 2:**

```
输入: [1,3,5,6], 2
输出: 1
```

错误解法：(目前不知道问题在哪)

```js
var searchInsert = function(nums, target) {
        let arr = [];
        index = nums.indexOf(target);
        if(index >=0)  return index;
        else if(index == -1){
            nums.push(target);
            arr = nums.sort(); ///原因sort不能排序
            arr =  [...new Set(arr)];
            return arr.indexOf(target);
        }
};
```

正确解法：

```js
var searchInsert = function(nums, target) {
    var index = nums.length;
    for(var i=0;i<nums.length;i++){
        if(nums[i] >= target){
            return i;
        }
         
    }
    return index;
};
```

### 