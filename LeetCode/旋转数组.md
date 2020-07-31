### 89. 旋转数组

知识点:splice会改变原数组;slice[start,end)不会改变原数组

给定一个数组，将数组中的元素向右移动 k 个位置，其中 k 是非负数。

示例 :

输入: [1,2,3,4,5,6,7] 和 k = 3
输出: [5,6,7,1,2,3,4]

解题-( 切片和插入 ):

```js
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var rotate = function(nums, k) {
  if (nums.length < 2 || k === 0) {
    return;
  }
  k = k % nums.length;
  const cut = nums.splice(nums.length - k, k);//splice返回的是被修改之后的数组
  nums.unshift(...cut);
};
```

解法二( 循环+切片和插入 )

```js
var rotate = function(nums, k) {
  var count = 0
  k %= nums.length
  while (count < k) {
    var popData = nums.pop()
    nums.unshift(popData)
    count ++
  }
}
```

解法三(一步切片,添加):

```js
var rotate = function(nums, k) {
    nums.splice(0, 0, ...nums.splice(nums.length-k))
    return nums;
};
```

### 