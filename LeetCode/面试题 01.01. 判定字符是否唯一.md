#### [面试题 01.01. 判定字符是否唯一](https://leetcode-cn.com/problems/is-unique-lcci/)

难度简单14

实现一个算法，确定一个字符串 `s` 的所有字符是否全都不同。

**示例 1：**

```
输入: s = "leetcode"
输出: false 
```

**示例 2：**

```
输入: s = "abc"
输出: true
```

### 解法

执行用时 :40 ms, 在所有 JavaScript 提交中击败了100.00%的用户

内存消耗 :34.1 MB, 在所有 JavaScript 提交中击败了100.00%的用户

```js
var isUnique = function(astr) {
    let arr = [];
    for (let i of astr){
        if(arr.indexOf(i) ===-1){
            arr.push(i)
        }else{
            return false
        }
    }
    return true
};
```

### 使用Set去重

```js
var isUnique = function(astr) {
    return new Set(astr).size === astr.length
};
```

