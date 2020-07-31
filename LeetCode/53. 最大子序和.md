## [53. 最大子序和](https://leetcode-cn.com/problems/maximum-subarray/)

给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

示例:

输入: [-2,1,-3,4,-1,2,1,-5,4],
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。

### 动态规划

将问题分为子问题一个一个解决

```js
var maxSubArray = function(nums) {
    //一段一段的子序列和
    var sum = 0;
    var max = nums[0];
    for(let num of nums) {
        if(sum > 0){
             // 前面累加的增长 > 0 那就加上我自己再看看
            sum += num;
        }else {
            // 前面累加的增长 < 0  前面的所有累加的增长都白干了，那就从这一次开始看后面的吧
            sum = num
        }
        // sum就等于从前面某一天到今天的增长,取最大的值
         max = Math.max(max, sum);
    } 
    return max;
};
```

### 分治法

首先将数组利用二分法递归分成左右两个数组。

算出左边数组从中间起的最大和，
算出右边数组从中间起的最大和，
算出他们的和，即穿过中间值的和，
判断这三个哪个最大，并存入maxsum中

最后递归判断出最大值。

```js
/**
 * @param {number[]} nums
 * @return {number}
 */
var maxSubArray = function(nums) {
    return divide(nums, 0, nums.length-1);
  };
   
  var divide = function(nums, l, r) {
    if (l === r) {
      return nums[l];
    }
    if (l === r-1) {
      return Math.max(nums[l], Math.max(nums[r], nums[l] + nums[r]));
    }
    
    let mid = parseInt((l + r) / 2);
    let lmax = divide(nums, l, mid-1);
    let rmax = divide(nums, mid+1, r);
    
    let mmax = nums[mid];  // 从中间开始计算
    let sum = mmax; // 用来求和
    for (let i = mid - 1; i >= l; i--) {
      sum += nums[i];
      mmax = Math.max(mmax, sum);
    }
    
    sum = mmax;
    for (let i = mid + 1; i <= r; i++) {
      sum += nums[i];
      mmax = Math.max(mmax, sum);
    }
    
    return Math.max(lmax, Math.max(rmax, mmax));
  };
  console.log(maxSubArray([-2,1,-3,4,-1,2,1,-5,4]))
```

### 贪心(最快)

之前的和（thisSum ）和当前元素（ nums[i]）相加，如果数值小于当前元素，肯定就没有用的必要了，直接使用现在的元素作为当前最大和（thisSum ）。

就是算出每次相加后的最优解。

```js
var maxSubArray = function(nums) {
    max = nums[0];
    sum = nums[0];
    for(let i=1;i<nums.length;i++){
        sum = sum + nums[i] >nums[i] ? sum+nums[i] : nums[i];
        max = Math.max(max,sum)
    }
    return max;
};
```

