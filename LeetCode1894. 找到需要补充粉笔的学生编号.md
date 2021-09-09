>一个班级里有 n 个学生，编号为 0 到 n - 1 。每个学生会依次回答问题，编号为 0 的学生先回答，然后是编号为 1 的学生，以此类推，直到编号为 n - 1 的学生，然后老师会重复这个过程，重新从编号为 0 的学生开始回答问题。
>
>给你一个长度为 n 且下标从 0 开始的整数数组 chalk 和一个整数 k 。一开始粉笔盒里总共有 k 支粉笔。当编号为 i 的学生回答问题时，他会消耗 chalk[i] 支粉笔。如果剩余粉笔数量 严格小于 chalk[i] ，那么学生 i 需要 补充 粉笔。
>
>请你返回需要 补充 粉笔的学生 编号 。
>
>**示例 1：**
>
>输入：chalk = [5,1,5], k = 22
>输出：0
>解释：学生消耗粉笔情况如下：
>- 编号为 0 的学生使用 5 支粉笔，然后 k = 17 。
>- 编号为 1 的学生使用 1 支粉笔，然后 k = 16 。
>- 编号为 2 的学生使用 5 支粉笔，然后 k = 11 。
>- 编号为 0 的学生使用 5 支粉笔，然后 k = 6 。
>- 编号为 1 的学生使用 1 支粉笔，然后 k = 5 。
>- 编号为 2 的学生使用 5 支粉笔，然后 k = 0 。
>编号为 0 的学生没有足够的粉笔，所以他需要补充粉笔。
>
>来源：[力扣（LeetCode）](https://leetcode-cn.com/problems/find-the-student-that-will-replace-the-chalk)

### 读题

1. 编号为 i 的学生回答问题会消耗 chalk[i] 支粉笔。

2. 如果 剩余粉笔数量 < chalk[i]，那么这第 i 个同学就要补充粉笔。

3. 以上过程可能会重复多轮次。

要求：返回需要补充粉笔的学生编号。



### 解题思路

n 个学生每一轮消耗粉笔的个数是 chalk 数组之和 sum，一轮消耗后可能 k 还有，所以消耗粉笔这一过程是可能会重复的。当然也可能不够一轮。

轮次可能会有很多，我们模拟每一轮递减会超时。所以就得简化这个模拟过程。

将粉笔数量 k % sum(每一轮消耗) 或得一个余数。这个余数肯定小于 sum。然后遍历 chalk ，模拟粉笔的消耗，待 chalk[i] > k 时 就可得到需要补充粉笔的学生编号。



<img src="https://tva1.sinaimg.cn/large/008i3skNly1guaw17aq9fj60nu0qaabr02.jpg" alt="image.png" style="zoom:50%;" />



### 代码

```cpp
class Solution {
public:
    int chalkReplacer(vector<int>& chalk, int k) {
        long long sum = 0;
        for(int i : chalk) sum += i; //求和
        k = k % sum; //取模简化模拟过程

        int size = chalk.size();
        for(int i = 0; i < size; i++) {
            if(chalk[i] > k) {
                return i;
            }
            k -= chalk[i];
        }
        return -1;
    }
};
```

