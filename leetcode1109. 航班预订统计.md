#### 前言

最近leetcode的每日刷题都是前缀和类的，比较有连贯性。没有上来搞个hard打击人。本题用到了差分、前缀和，好记性不烂笔头，笔记之。欢迎点赞👍🏻

> 这里有 n 个航班，它们分别从 1 到 n 进行编号。
>
> 有一份航班预订表 bookings ，表中第 i 条预订记录 bookings[i] = [firsti, lasti, seatsi] 意味着在从 firsti 到 lasti （包含 firsti 和 lasti ）的 每个航班 上预订了 seatsi 个座位。
>
> 请你返回一个长度为 n 的数组 answer，其中 answer[i] 是航班 i 上预订的座位总数。
>
> 来源：力扣（LeetCode）
>
> 链接：https://leetcode-cn.com/problems/corporate-flight-bookings
>
> 示例 1：
>
> 输入：bookings = [[1,2,10],[2,3,20],[2,5,25]], n = 5
>
> 输出：[10,55,45,25,25]
>
> 解释：
>
> 航班编号 1 2 3 4 5
>
> 预订记录 1 ： 10 10
>
> 预订记录 2 ： 20 20
>
> 预订记录 3 ： 25 25 25 25
>
> 总座位数： 10 55 45 25 25
>
> 因此，answer = [10,55,45,25,25]
>
> 1 <= n <= 2 * 104
>
> 1 <= bookings.length <= 2 * 104
>
> bookings[i].length == 3
>
> 1 <= firsti <= lasti <= n
>
> 1 <= seatsi <= 104

***

#### 读题

1. 有n个航班，起始位置是1。
2. `bookings[i] = [startSeats, ensSeats, seats]`表示从 `startSeats` 到 `ensSeats` （**包含** `firsti` 和 `lasti` ）的每个航班上预订 `seatsi` 个座位。

要求：返回一个数组，里面的元素是每个航班预定的座位数。

#### 解题

看这个题，直观的思维就是一个一个遍历bookings。然后去累加每一个booking[2]。但是这样会超时。

那我们把题例简化下: `bookings = [[1, 2 , 10]]; n = 5;`

就变成了

|  1   |  2   |  3   |  4   |  5   |
| :--: | :--: | :--: | :--: | :--: |
|  10  |  10  |  0   |  0   |  0   |

插一句，首先了解一个概念，什么是差分数组？

差分数组：对应的概念是前缀和数组。例如`[1, 2, 3, 4]`的前缀和结果就是`[1, 3, 6, 10]`， 其差分数组就是`[1, 1, 1, 1]`。

ok继续，如果我们只标记1和3呢？

|        |  1   |  2   |  3   |  4   |  5   |
| :----: | :--: | :--: | :--: | :--: | :--: |
|        |  10  |      | -10  |      |      |
| 前缀和 |      | +10  |      |      |      |
|  结果  |  10  |  10  |  0   |  0   |  0   |

所以就可以转换为标记1到3这个区间，增量10。只需要操作这个区间的两端，区间左端加10， 区间右端+1位置-10。之后其他区间也这么标记。构造差分数组，再依据差分数组计算前缀和就可以得到题目想要的结果。即采用差分的思想来模拟区间的增量。

再回到本题示例`bookings =  [[1, 2, 10], [2, 3, 20], [2, 5, 25]]; n = 5`

![WeChat81e64f69bbef4a46a672bde73fbe0e00](https://tva1.sinaimg.cn/large/008i3skNly1gtzytydnw9j60u013jtd302.jpg)

#### 代码

##### 个人题解

**暴力法(超时)**

```cpp
class Solution {
public:
		vector<int> corpFlightBookings(vector<vector<int>>& bookings, int n) {
				vector<int> res(n);
				for (auto &b : bookings) {
						for (int i = b[0] - 1; i < b[1]; i++) {
								res[i] += b[2];
						}
				}
				return res;
		}
};
```

**个人题解（差分、前缀和）**

```c++
class Solution {
public:
    vector<int> corpFlightBookings(vector<vector<int>>& bookings, int n) {
        vector<int> res(n + 1);
        int l = 0, r = 0, a = 0;
        for (auto & b : bookings) {
            l = b[0] - 1;
            r = b[1] - 1;
            a = b[2];
            res[l] += a;
            res[r + 1] -= a;
        }
        vector<int> fCount(n);

        for (int i = 0; i < n; i++) {
            if (i == 0) {
                fCount[i] = res[0];
            }else {
                fCount[i] = fCount[i - 1] + res[i];
            }
        }
        return fCount;
    }
};
```

##### 官方题解

```c++
class Solution {
public:
    vector<int> corpFlightBookings(vector<vector<int>>& bookings, int n) {
        vector<int> nums(n);
        for (auto& booking : bookings) {
            nums[booking[0] - 1] += booking[2];
            if (booking[1] < n) {
                nums[booking[1]] -= booking[2];
            }
        }
        for (int i = 1; i < n; i++) {
            nums[i] += nums[i - 1];
        }
        return nums;
    }
};
```

