#### [1047. 删除字符串中的所有相邻重复项](https://leetcode-cn.com/problems/remove-all-adjacent-duplicates-in-string/)

难度简单54

给出由小写字母组成的字符串 `S`，**重复项删除操作**会选择两个相邻且相同的字母，并删除它们。

在 S 上反复执行重复项删除操作，直到无法继续删除。

在完成所有重复项删除操作后返回最终的字符串。答案保证唯一。

 

**示例：**

```
输入："abbaca"
输出："ca"
解释：
例如，在 "abbaca" 中，我们可以删除 "bb" 由于两字母相邻且相同，这是此时唯一可以执行删除操作的重复项。之后我们得到字符串 "aaca"，其中又只有 "aa" 可以执行重复项删除操作，所以最后的字符串为 "ca"。
```

 ### 解法

递归

```js
/**
 * @param {string} S
 * @return {string}
 */
var removeDuplicates = function(S) {
    S = [...S]
    for(let i=0;i<S.length;i++){
        if(S[i] === S[i+1]){
            S.splice(i,2);
            return  removeDuplicates(S);
        }
    }
    return S.join('');
};
```

栈:利用一个数组,来判断

```js
/**
 * @param {string} S
 * @return {string}
 */
var removeDuplicates = function(S) {
    let arr = [];
    for(let item of S){
      if(arr.length){
          if(arr[arr.length-1] === item){
              arr.pop();
          }else{
              arr.push(item)
          }
      }else{
          arr.push(item)
      }
    }
    return arr.join('')
};
```

