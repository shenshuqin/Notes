#### [1103. 分糖果 II](https://leetcode-cn.com/problems/distribute-candies-to-people/)

排排坐，分糖果。

我们买了一些糖果 candies，打算把它们分给排好队的 n = num_people 个小朋友。

给第一个小朋友 1 颗糖果，第二个小朋友 2 颗，依此类推，直到给最后一个小朋友 n 颗糖果。

然后，我们再回到队伍的起点，给第一个小朋友 n + 1 颗糖果，第二个小朋友 n + 2 颗，依此类推，直到给最后一个小朋友 2 * n 颗糖果。

重复上述过程（每次都比上一次多给出一颗糖果，当到达队伍终点后再次从队伍起点开始），直到我们分完所有的糖果。注意，就算我们手中的剩下糖果数不够（不比前一次发出的糖果多），这些糖果也会全部发给当前的小朋友。

返回一个长度为 num_people、元素之和为 candies 的数组，以表示糖果的最终分发情况（即 ans[i] 表示第 i 个小朋友分到的糖果数）。

 

示例 1：

输入：candies = 7, num_people = 4
输出：[1,2,3,1]
解释：
第一次，ans[0] += 1，数组变为 [1,0,0,0]。
第二次，ans[1] += 2，数组变为 [1,2,0,0]。
第三次，ans[2] += 3，数组变为 [1,2,3,0]。
第四次，ans[3] += 1（因为此时只剩下 1 颗糖果），最终数组变为 [1,2,3,1]。

示例 2：

输入：candies = 10, num_people = 3
输出：[5,2,3]
解释：
第一次，ans[0] += 1，数组变为 [1,0,0]。
第二次，ans[1] += 2，数组变为 [1,2,0]。
第三次，ans[2] += 3，数组变为 [1,2,3]。
第四次，ans[0] += 4，最终数组变为 [5,2,3]。

#### 解题

1.解题错误输入(90,4),应该输出 **[27,18,21,24]**  ,错误输出了 **[28,17,21,24]** 

原因分析:发第二遍的糖果,是上一次的+1,和剩余糖果比较

```js
    var distributeCandies = function(candies, num_people) {
        let assign_arr = new Array(num_people).fill(0);
        let times = 0,assign_num = 0;
        while(candies){
            let temp = times++%num_people;
            assign_num = assign_num>candies?candies:assign_num+1;
            assign_arr[temp] = assign_arr[temp] +assign_num;
            candies -=assign_num;
        }
        return assign_arr;
};


//修改后代码:
var distributeCandies = function(candies, num_people) {
    let assign_arr = new Array(num_people).fill(0);
    let times = 0,assign_num = 0;
    while(candies){
        assign_num = assign_num+1 > candies? candies : assign_num+1;
        assign_arr[times++%num_people] += assign_num;
        candies -=assign_num;
    }
    return assign_arr;
}

console.log(distributeCandies(90,4));
```



2.正解:此方法可以通过

```js
    var distributeCandies = function(candies, num_people) {
        let assign_arr = new Array(num_people).fill(0);
        let times = 0,assign_num = 1;
        while(candies){
            let temp = times++%num_people;
            assign_num = assign_num>candies?candies:assign_num;
            assign_arr[temp] = assign_arr[temp] +assign_num;
            candies -=assign_num++;
        }
        return assign_arr;
    };
```