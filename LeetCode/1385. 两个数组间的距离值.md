#### [1385. 两个数组间的距离值](https://leetcode-cn.com/problems/find-the-distance-value-between-two-arrays/)

给你两个整数数组 `arr1` ， `arr2` 和一个整数 `d` ，请你返回两个数组之间的 **距离值** 。

「**距离值**」 定义为符合此描述的元素数目：对于元素 `arr1[i]` ，不存在任何元素 `arr2[j]` 满足 `|arr1[i]-arr2[j]| <= d` 。

 

**示例 1：**

```
输入：arr1 = [4,5,8], arr2 = [10,9,1,8], d = 2
输出：2
解释：
对于 arr1[0]=4 我们有：
|4-10|=6 > d=2 
|4-9|=5 > d=2 
|4-1|=3 > d=2 
|4-8|=4 > d=2 
对于 arr1[1]=5 我们有：
|5-10|=5 > d=2 
|5-9|=4 > d=2 
|5-1|=4 > d=2 
|5-8|=3 > d=2
对于 arr1[2]=8 我们有：
|8-10|=2 <= d=2
|8-9|=1 <= d=2
|8-1|=7 > d=2
|8-8|=0 <= d=2
```

**示例 2：**

```
输入：arr1 = [1,4,2,3], arr2 = [-4,-3,6,10,20,30], d = 3
输出：2
```

### 解法

一开始的错误点:

```js
var findTheDistanceValue = function(arr1, arr2, d) {
    let num = 0,flag = true;
    for(let i =0;i<arr1.length;i++){
        for(let j=0;j<arr2.length;j++){
            if(Math.abs(arr1[i]-arr2[j]) <=d){
                flag = false;
                break;
            }
        }
        if(flag){
          num++;
        }
       // flag = true; ///一开始忘记再次初始化flag = true造成错误
    }
    return num;
};
console.log(findTheDistanceValue([1,4,2,3],[-4,-3,6,10,20,30],3))
// 可行:1 2----2
//输出:1====1
```



执行用时 :72 ms, 在所有 JavaScript 提交中击败了80.23%的用户

内存消耗 :34.7 MB, 在所有 JavaScript 提交中击败了100.00%的用户

```js
/**
 * @param {number[]} arr1
 * @param {number[]} arr2
 * @param {number} d
 * @return {number}
 */
var findTheDistanceValue = function(arr1, arr2, d) {
    let num = 0;
    for(let i =0;i<arr1.length;i++){
        let flag = true;
        for(let j=0;j<arr2.length;j++){
            if(Math.abs(arr1[i]-arr2[j]) <=d){
                flag = false;
                break;
            }
        }
        if(flag){
           num++;
        }
    }
    return num;
};
```

