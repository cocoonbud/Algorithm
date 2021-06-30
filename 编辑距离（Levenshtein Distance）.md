### 前言

最近翻开之前一本工作笔记，回想起之前做一个搜索功能，其中有用到计算相似度时用过编辑距离算法。记得也是leetcode hard难度，本文权当个回顾吧。

#### 简介

Levenshtein距离，用于计算两个字符串之间的编辑距离。编辑距离的一种。是指两个字符串之间，由一个转换成另一个所需的最少编辑操作次数。算法概念是俄罗斯科学家弗拉基米尔·莱文斯坦（Levenshtein · Vladimir I）于1965年提出。允许的编辑操作包括：替换、插入、删除。

### 算法实现

#### 1. 设定dp数组意义

dp\[i][j]代表的意思为：以i - 1为结尾的字符串w1和以j - 1为结尾的字符串w2，最近的编辑距离记为dp\[i][j]。

#### 2. 推导过程

比较w1在i - 1位置和w2在j - 1位置的字符，分为两种情况。

##### 1. 不等

* 插入

例如w1 = "ab", w2 = "bca"。

&emsp;      若在w1上加字符：为了使使w[i - 1]与w2[j - 1]字符相等，那么就需要在w1的i - 1位置插入一个"b"，w1的长度增加1。也就是说此时以i-2为结尾的w1和以j-1位结尾的w2的最近编辑距离增加了一个插入操作。

即 dp\[i][j]= dp\[i - 1][j] + 1;

&emsp;      若在w2上加字符：使w[i - 1]与w2[j - 1]相同，那么就需要在w2的j - 1位置插入一个"a"，w2的长度增加1。也就是说此时以i - i为结尾的w1和以j - 2位结尾的w1的最近编辑距离增加了一个插入操作。

即 dp\[i][j]= dp\[i][j - 1] + 1;

* 替换

w1替换w1[i - 1]，使其与word2[j - 1]相同，那么以下标i - 2为结尾的w1与i - 2为结尾的w2的最近编辑距离加一个替换字符的操作。

即 dp\[i][j] = dp\[i - 1][j - 1] + 1;

* 删除

删除与插入其实所需要的操作数是一样的。在w1上删除一个字符，等同于在w2上插入一个字符的操作数。

以上三种操作，取最少编辑距离即：dp\[i][j] = min({dp\[i - 1][j], dp\[i][j - 1], dp\[i - 1][j - 1]}) + 1;

##### 2. 相等

若w1[i - 1] == w2[j - 1]，无需编辑。

即 dp\[i][j] = dp\[i - 1][j - 1];

通过以上两种情况，可推导出动态转移方程：
$$
dp[i][j]=\left\{\begin{array}{ll}dp[i-1][j-1] & \ word1[i]= word2[j] \\\\min (dp[i-1][j], \ dp[i-1][j-1], \ dp[i][j-1])+1 & \ word1[i] \neq word2[j]\end{array}\right.
$$


边界情况:

一个空串和一个非空串的编辑距离为dp\[i][0] = i和dp\[0][j] = j，dp\[i][0]相当于对w1执行i次删除操作，dp\[0][j]相当于对w1执行j次操作。

````C++
class Solution {
public:
    int minDistance(string word1, string word2) {
        int size1 = word1.size(), size2 = word2.size();
        vector<vector<int>> dp(size1 + 1, vector<int>(size2 + 1, 0));
        for (int i = 0; i <= size1; i++) {
            dp[i][0] = i;
        }

        for (int i = 0; i <= size2; i++) {
            dp[0][i] = i;
        }

        for (int i = 1; i <= size1; i++) {
            for (int j = 1; j <= size2; j++) {
                if (word1[i - 1] == word2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1];
                }else {
                    dp[i][j] = min({dp[i][j - 1], dp[i - 1][j - 1], dp[i - 1][j]}) + 1;
                }
            }
        }
        return dp[size1][size2];
    }
};
````



### 其他

1. 用户的一些输入模糊匹配。如Elasticsearch的Fuzzy query。

2. 如果某场景要求string的前部分越相似分数越高。例如对于计算"12A45"，"12345A"和"12345"的Levenshtein距离值是相同的。此时可用Jaro–Winkler distance。Jaro-Winkler distance会给起始部分就相同的字符串更高的分数。有兴趣的小伙伴可以去google下具体实现。