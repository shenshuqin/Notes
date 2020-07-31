#### [922. 按奇偶排序数组 II](https://leetcode-cn.com/problems/sort-array-by-parity-ii/)

难度简单84

给定一个非负整数数组 `A`， A 中一半整数是奇数，一半整数是偶数。

对数组进行排序，以便当 `A[i]` 为奇数时，`i` 也是奇数；当 `A[i]` 为偶数时， `i` 也是偶数。

你可以返回任何满足上述条件的数组作为答案。

 

**示例：**

```
输入：[4,2,5,7]
输出：[4,5,2,7]
解释：[4,7,2,5]，[2,5,4,7]，[2,7,4,5] 也会被接受。
```

### 暴力解法

```js
var sortArrayByParityII = function(A) {
 let newArr = [],head = 0,tail= A.length/2;
 let a =[],b =[],arr=[];
 for(let j =0;j<A.length;j++){
     if(A[j]%2 === 0){
         a.push(A[j])
     }else{
        b.push(A[j])
     }
 }
 arr = [...a,...b]
 if(arr.length === 2){
     return arr
 }
 for(let i=0;i<arr.length;i++){
    if(i%2 === 0){
        newArr.push(arr[head])
        head++
    }else{
        newArr.push(arr[tail])
        tail++
    }
 }
 return newArr
};
```

### 改进版暴力

```js
/**
 * @param {number[]} A
 * @return {number[]}
 */
/**
 * @param {number[]} A
 * @return {number[]}
 */
var sortArrayByParityII = function(A) {
    let arr = []
    let odd = 1
    let even = 0
    A.forEach(item => {
        if(item % 2 == 0){
            arr[even] = item
            even += 2
        }else{
            arr[odd] = item
            odd += 2
        }
    })
    return arr
};
```

### 别人优秀代码

```js
var sortArrayByParityII = function(A) {
    let i = 0,
        j = 1;
    for(; i < A.length; i += 2){
        if(A[i] % 2 == 0)continue;//当A[i] % 2 == 1时会到下面的while循环看j
        while(A[j] % 2 == 1)j += 2;
        [A[i],A[j]]=[A[j],A[i]];//ES6在引入了数组解构的概念，值互换会变得更加方便
    }
    return A;
}
```

