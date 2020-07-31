#### [1370. 上升下降字符串](https://leetcode-cn.com/problems/increasing-decreasing-string/)

给你一个字符串 `s` ，请你根据下面的算法重新构造字符串：

1. 从 `s` 中选出 **最小** 的字符，将它 **接在** 结果字符串的后面。
2. 从 `s` 剩余字符中选出 **最小** 的字符，且该字符比上一个添加的字符大，将它 **接在** 结果字符串后面。
3. 重复步骤 2 ，直到你没法从 `s` 中选择字符。
4. 从 `s` 中选出 **最大** 的字符，将它 **接在** 结果字符串的后面。
5. 从 `s` 剩余字符中选出 **最大** 的字符，且该字符比上一个添加的字符小，将它 **接在** 结果字符串后面。
6. 重复步骤 5 ，直到你没法从 `s` 中选择字符。
7. 重复步骤 1 到 6 ，直到 `s` 中所有字符都已经被选过。

在任何一步中，如果最小或者最大字符不止一个 ，你可以选择其中任意一个，并将其添加到结果字符串。

请你返回将 `s` 中字符重新排序后的 **结果字符串** 。

 

**示例 1：**

```
输入：s = "aaaabbbbcccc"
输出："abccbaabccba"
解释：第一轮的步骤 1，2，3 后，结果字符串为 result = "abc"
第一轮的步骤 4，5，6 后，结果字符串为 result = "abccba"
第一轮结束，现在 s = "aabbcc" ，我们再次回到步骤 1
第二轮的步骤 1，2，3 后，结果字符串为 result = "abccbaabc"
第二轮的步骤 4，5，6 后，结果字符串为 result = "abccbaabccba"
```

### 解法

1暴力(新建一个对象,遍历对象key值)

```js
/**
 * @param {string} s
 * @return {string}
 */
var sortString = function(s) {
    let letterMap = {};
    let result = '';
    let flag = false;
    if(s === '') return s
    s.split('').forEach(s => {
      letterMap[s] ? letterMap[s]++ : letterMap[s] = 1;
    });
    while(!flag) {
      let keys = Object.keys(letterMap);
      keys = keys.sort((a,b) => a < b ? -1 : 1);
      flag = true;
      keys.forEach(key=> {
        if(letterMap[key] > 0) {
          result = result.concat(key);
          flag = false;
        }
        letterMap[key]--;
      });
      let sortKeys = Object.keys(letterMap);
      sortKeys = sortKeys.sort((a,b) => a > b ? -1 : 1);
      sortKeys.forEach(key=> {
        if(letterMap[key] > 0) {
          result = result.concat(key);
          flag = false;
        }
        letterMap[key]--;
      });
    }
    return result;
};
```

2.递归

```js
var sortString = function (s) {
    s = s.split('').sort((a, b) => a.charCodeAt() - b.charCodeAt());
    function handler(ss, flag) {
        let res = '';
        let current = '';
        if (ss.length === 0) return '';
        ss = ss.filter((item) => {
            if (item !== current) {
                !flag && (res = res + item);
                flag && (res = item + res);
                current = item;
                return false;
            }
            return true
        })
        console.log(ss)
        flag = !flag;
        return res + handler(ss, flag);
    }
    return handler(s, false)
};

console.log(sortString("aaaabbbbcccc"))

```

