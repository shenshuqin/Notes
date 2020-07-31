#### [1304. 和为零的N个唯一整数](https://leetcode-cn.com/problems/find-n-unique-integers-sum-up-to-zero/)

给你一个整数 `n`，请你返回 **任意** 一个由 `n` 个 **各不相同** 的整数组成的数组，并且这 `n` 个数相加和为 `0` 。

**示例 1：**

```
输入：n = 5
输出：[-7,-1,1,3,4]
解释：这些数组也是正确的 [-5,-1,1,2,3]，[-3,-1,2,-2,4]。
```

**示例 2：**

```
输入：n = 3
输出：[-1,0,1]
```

**示例 3：**

```
输入：n = 1
输出：[0]
```

解法一:

```js
var sumZero = function(n) {
        let res = [],sum = 0;
        for(let i=0;i<n-1;i++){
            res.push(i)
            sum += i
        }
        // console.log(sum)
        res.push(-sum)
        return res
};
```

解法二:

```js
 var sumZero = function(n) {
        let res = []
        let i
        if( n % 2 == 0) {
            i = 1
        } else {
            i = 0
        }
        for(i; i <= (n/2); i++) {
            if(i === 0) {
                res.push(i)
            } else {
                res.push(i)
                res.push(-i)
            }
        }
        return res
```

