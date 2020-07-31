#### [821. 字符的最短距离](https://leetcode-cn.com/problems/shortest-distance-to-a-character/)

难度简单111

给定一个字符串 `S` 和一个字符 `C`。返回一个代表字符串 `S` 中每个字符到字符串 `S` 中的字符 `C` 的最短距离的数组。

**示例 1:**

```
输入: S = "loveleetcode", C = 'e'
输出: [3, 2, 1, 0, 1, 0, 0, 1, 2, 2, 1, 0]
```

### 解法

```js
var shortestToChar = function(S, C) {
  let newArr = [],intance = 0;
  for(let i =0;i<S.length;i++){
    if(S[i] === C){
      newArr.push(i)
    }
  }
 let intanceArr = [],res=[];
  for(let i=0;i<S.length;i++){
    newArr.forEach((item,index)=>{
      let min = Math.abs(item - i);
      intanceArr.push(min);
    })
    res.push(Math.min(...intanceArr))
    intanceArr.length = 0;
  }
  return res
};
////优化 效率并没有提高
var shortestToChar = function(S, C) {
  let hash = []
  let ret = []
  //记录c字符在s中的位置
  for(let i = 0; i < S.length; ++i)
  {
      if(S[i] == C)
      {
          hash.push(i)
      }
  }
  for(let i = 0; i < S.length;++i)
  {
      let arr = hash.map(item=>Math.abs(item-i))
      console.log(arr)
      ret.push(Math.min(...arr))
  }
  return ret
};
```

