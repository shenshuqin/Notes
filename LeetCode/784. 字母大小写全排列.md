#### [784. 字母大小写全排列](https://leetcode-cn.com/problems/letter-case-permutation/)

难度简单158

给定一个字符串`S`，通过将字符串`S`中的每个字母转变大小写，我们可以获得一个新的字符串。返回所有可能得到的字符串集合。

```
示例:
输入: S = "a1b2"
输出: ["a1b2", "a1B2", "A1b2", "A1B2"]

输入: S = "3z4"
输出: ["3z4", "3Z4"]

输入: S = "12345"
输出: ["12345"]
```

### 解

```js
/**
 * @param {string} S
 * @return {string[]}
 */
var letterCasePermutation = function(S) {
  let arr = [];
  for (let i = 0; i < S.length; i++) {
    if (i === 0) {
      arr = [...new Set([S[0].toLowerCase(), S[0].toUpperCase()])];
    } else {
      arr = arr.filter(item => item.length === i);
      // console.log(arr,"arr")
      const upper = S[i].toUpperCase();
      const lower = S[i].toLowerCase();
      const length = arr.length;
      for (let i = 0; i < length; i++) {
        if (arr.findIndex(item => item === arr[i] + upper) === -1) {
          arr.push(arr[i] + upper);
        }
        if (arr.findIndex(item => item === arr[i] + lower) === -1) {
          arr.push(arr[i] + lower);
        }
      }
    }
  }
  return arr.filter(item => item.length === S.length);
};
```

回溯

```js
const letterCasePermutation = S => {
  const res = [];
  const backtrack = (start, s) => {
    res.push(s);
    for (let i = start; i < s.length; i++) {
      if (s[i] >= 'a' && s[i] <= 'z') {
        backtrack(i + 1, s.slice(0, i) + s[i].toUpperCase() + s.slice(i + 1));
      } else if (s[i] >= 'A' && s[i] <= 'Z') {
        backtrack(i + 1, s.slice(0, i) + s[i].toLowerCase() + s.slice(i + 1));
      }
    }
  };
  backtrack(0, S);
  return res;
};

```

