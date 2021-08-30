#### 前言

按权重随机选择在开发中是一个相对于其他leetcode题来说比较实用，且在日常开发中会见到和使用的算法，还是有现实场景应用的。所以今天leetcode每日刷题AC后，觉得还是有必要做个笔记总结下。

##### [Leetcode 528.按权重随机选择](https://leetcode-cn.com/problems/random-pick-with-weight/solution/cqian-zhui-he-cai-tu-by-terence123-pvvs/)

> 给定一个正整数数组 w ，其中 w[i] 代表下标 i 的权重（下标从 0 开始），请写一个函数 pickIndex ，它可以随机地获取下标 i，选取下标 i 的概率与 w[i] 成正比。
>
> 例如，对于 w = [1, 3]，挑选下标 0 的概率为 1 / (1 + 3) = 0.25 （即，25%），而选取下标 1 的概率为 3 / (1 + 3) = 0.75（即，75%）。
>
> 也就是说，选取下标 i 的概率为 w[i] / sum(w) 。
>
> 来源：[力扣（LeetCode）](https://leetcode-cn.com/problems/random-pick-with-weight)
>
> **示例**
>
> 输入：
>
> ["Solution","pickIndex"]
>
> [[[[1]],[]]
>
> 输出：
>
> [null,0]
>
> 解释：
>
> Solution solution = new Solution([1]);
>
> solution.pickIndex(); // 返回 0，因为数组中只有一个元素，所以唯一的选择是返回下标 0。
>
> **提示**
>
> 1 <= w.length <= 10000、
>
> 1 <= w[i] <= 10^5
>
> pickIndex 将被调用不超过 10000 次

***

#### 读题：

1. w[i] 代表下标 i 的权重。

2. 选下标 i 的概率为 w[i] / sum(w)，即此权重值除以整个数组的累加和。

要求：写个函数pickIndex，使能够随机获得下标 i ，选取下标 i 的概率与 w[i] 成正比。

##### 场景例子

比如：假如有10个游戏道具，有屠龙宝刀，也有烧火棍等等。需要随机抽出一个发给玩家。由于道具的稀缺度，价值等不同，所以抽取的概率肯定不希望是是平均的。也就是高价值高权重获得的概率低，低价值低权重获得的概率高。

要实现这种效果，就需要给每个道具指定一个权重。比如烧火棍比较垃圾给个权重80，屠龙宝刀稀有给个权重20。即80%的概率会抽到烧火棍，这个就是基于权重的随机算法。

本题意思相对比较简单，与上边的场景例子期望相反。期望对数组的元素随机采样，元素的权重比越高，出现的概率越高。

#### 解题

以 w = [1, 2, 4, 3]为例：

sum 是10。相当于有10个矩形格子。按照1、2、4、3来划分4个区间。

我们在这10之内随机取一个数。

例如1，落在了第1个区间，对应的index就是0。

例如2，落在了第2个区间，对应的index就是1。

例如3，落在了第2个区间，对应的index就是1。

例如4，落在了第3个区间，对应的index就是2。

例如5，落在了第3个区间，对应的index就是2。

例如6，落在了第3个区间，对应的index就是2。

例如7，落在了第3个区间，对应的index就是2。

例如8，落在了第4个区间，对应的index就是3。

.

.

![截屏2021-08-30 上午1.48.57](https://tva1.sinaimg.cn/large/008i3skNgy1gtyzgtfktrj60no0mwgnc02.jpg)

其实说白了，原本每一个数的初始概率都为1 / 10，w 数组内某个元素的值越大，权重越大，你期望它被选中的概率越大，那么这个值对应的区间范围就要越大。

所以就像 w[2] = 4，我们就让它占据4个格子，那它占用的格子数是最多了，被选中的概率就最大了。

#### 代码

##### 个人题解：

```c++
class Solution {
public:
    Solution(vector<int>& w) {
        preSum = w;
        int size = w.size();
        for (int i = 1; i < size; i++) {
            preSum[i] += preSum[i - 1];
        }
    }
    
    int pickIndex() {
        int p = rand() % preSum.back() + 1;
        return lower_bound(preSum.begin(), preSum.end(), p) - preSum.begin();
    } 
private:
    vector<int> preSum;
};

/**
 * Your Solution object will be instantiated and called as such:
 * Solution* obj = new Solution(w);
 * int param_1 = obj->pickIndex();
 */
```

##### 官方题解：

```c++
class Solution {
private:
    mt19937 gen;
    uniform_int_distribution<int> dis;
    vector<int> pre;

public:
    Solution(vector<int>& w): gen(random_device{}()), dis(1, accumulate(w.begin(), w.end(), 0)) {
        partial_sum(w.begin(), w.end(), back_inserter(pre));
    }
    
    int pickIndex() {
        int x = dis(gen);
        return lower_bound(pre.begin(), pre.end(), x) - pre.begin();
    }
};
```



时间复杂度：

1. Solution时间复杂度为 O(n)

2. pickIndex里的lower_bound原理是二分查找，时间复杂度为 O(logn)

空间复杂度：O(n)

#### 拓展

[概率加权的随机抽样 (Weighted Random Sampling) – A-Res 蓄水池算法](http://live.aulddays.com/tech/17/weighted-random-sampling-reservoir-algorithm.htm)