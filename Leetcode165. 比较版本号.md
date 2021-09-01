#### 题目

>给你两个版本号 version1 和 version2 ，请你比较它们。
>
>版本号由一个或多个修订号组成，各修订号由一个 '.' 连接。每个修订号由 多位数字 组成，可能包含 前导零 。每个版本号至少包含一个字符。修订号从左到右编号，下标从 0 开始，最左边的修订号下标为 0 ，下一个修订号下标为 1 ，以此类推。例如，2.5.33 和 0.1 都是有效的版本号。
>
>比较版本号时，请按从左到右的顺序依次比较它们的修订号。比较修订号时，只需比较 忽略任何前导零后的整数值 。也就是说，修订号 1 和修订号 001 相等 。如果版本号没有指定某个下标处的修订号，则该修订号视为 0 。例如，版本 1.0 小于版本 1.1 ，因为它们下标为 0 的修订号相同，而下标为 1 的修订号分别为 0 和 1 ，0 < 1 。
>
>返回规则如下：
>
>如果 version1 > version2 返回 1，
>如果 version1 < version2 返回 -1，
>除此之外返回 0。
>
>**示例 1：**
>
>输入：version1 = "1.01", version2 = "1.001"
>输出：0
>解释：忽略前导零，"01" 和 "001" 都表示相同的整数 "1"

***

#### 读题

1. 从左到右依次比较

2. 忽略前导零 1与001相等

3. v1 > v2 return 1; v1 < v2 return -1。 else return 0

#### 解题思路

读完此题，直观的就是按照这个 `'.'` 来分段比较。因为是string，所以我们自然的就想到要把`'.'`分割开的每一段转换成为数字来比较。

接下来就是具体怎么实施了。

其中关于连续的string转换成数字这点

```c++
while (idx1 < size1 && version1[idx1] != '.') {
     a = a * 10 + version1[idx1] - '0';
     idx1++;
}
```

再啰嗦一点具体一点就是：

1. 定义两个指针 idx1 和 idx2 ， 分别去遍历两个string。

2. 两个指针分别遍历两个字符串，将每个小数点'.'分隔开的修订号解析成数字a、b，并进行大小比较：a > b ? 1 : -1。

3. idx1++，idx2++，reset 掉临时的 a、b, 再次进入步骤2。

4. 如果两个字符串任意一个遍历完，说明相等，return 0。

<img src="https://tva1.sinaimg.cn/large/008i3skNgy1gu0hq2a2qij60k60hm75s02.jpg" alt="截屏2021-09-01 上午1.16.44" style="zoom:80%;" />

(这题Leetcode medium，够不上吧...

### 代码

#### 个人题解

```c++
class Solution {
public:
    int compareVersion(string version1, string version2) {
        int idx1 = 0, idx2 = 0, size1 = version1.size(), size2 = version2.size(), a = 0, b = 0;
        while (idx1 < size1 || idx2 < size2) {
            a = 0, b = 0;
            while (idx1 < size1 && version1[idx1] != '.') {
                a = a * 10 + version1[idx1] - '0';
                idx1++;
            }
            
            while (idx2 < size2 && version2[idx2] != '.') {
                b = b * 10 + version2[idx2] - '0';
                idx2++;
            }
        
            if (a != b) {
                return a > b ? 1 : -1;
            }
            idx1++;
            idx2++;
        }
        return 0;
    }
};
```

- 时间复杂度：O(n+m)， n : version1 长度，m : version2 长度。
- 空间复杂度：O(1)

#### 使用 atoi 函数

```c++
class Solution {
public:
    int compareVersion(string version1, string version2) {
        int idx1 = 0, idx2 = 0, size1 = version1.size(), size2 = version2.size();
        int cur1 = 0, cur2 = 0, a = 0, b = 0;
        while (idx1 < size1 || idx2 < size2) {
		    cur1 = idx1, cur2 = idx2;                    
            while (cur1 < size1 && version1[cur1] != '.') cur1++;  
            while (cur2 < size2 && version2[cur2] != '.') cur2++;

            a = (idx1 == cur1) ? 0 : atoi(version1.substr(idx1, cur1 - idx1).c_str());
            b = (idx2 == cur2) ? 0 : atoi(version2.substr(idx2, cur2 - idx2).c_str());

            if (a != b) {
                return a > b ? 1 : -1;
            }

            idx1 = cur1 + 1;
            idx2 = cur2 + 1;
        }
        return 0;
    }
};
```

#### 巧用stringstream

```c++
class Solution {
public:
    int compareVersion(string version1, string version2) {
        stringstream s1(version1), s2(version2);
        int n1 = 0, n2 = 0;
        char c;
        while(bool(s1 >> n1) +  bool(s2 >> n2)) {
		    if (n1 != n2) {
            return n1 > n2 ? 1 : -1;
            }        
            n1 = 0;
            n2 = 0;
            s1 >> c;
            s2 >> c;
        }
        return 0;
    }
};
```

#### 官方题解

```c++
class Solution {
public:
    int compareVersion(string version1, string version2) {
        int n = version1.length(), m = version2.length();
        int i = 0, j = 0;
        while (i < n || j < m) {
            int x = 0;
            for (; i < n && version1[i] != '.'; ++i) {
                x = x * 10 + version1[i] - '0';
            }
            ++i; // 跳过点号
            int y = 0;
            for (; j < m && version2[j] != '.'; ++j) {
                y = y * 10 + version2[j] - '0';
            }
            ++j; // 跳过点号
            if (x != y) {
                return x > y ? 1 : -1;
            }
        }
        return 0;
    }
};
```

[作者：LeetCode-Solution](https://leetcode-cn.com/problems/compare-version-numbers/solution/bi-jiao-ban-ben-hao-by-leetcode-solution-k6wi/)