>给定一个单词数组和一个长度 *maxWidth*，重新排版单词，使其成为每行恰好有 *maxWidth* 个字符，且左右两端对齐的文本。
>
>你应该使用“贪心算法”来放置给定的单词；也就是说，尽可能多地往每行中放置单词。必要时可用空格 `' '` 填充，使得每行恰好有 *maxWidth* 个字符。
>
>要求尽可能均匀分配单词间的空格数量。如果某一行单词间的空格不能均匀分配，则左侧放置的空格数要多于右侧的空格数。
>
>文本的最后一行应为左对齐，且单词之间不插入**额外的**空格。
>
>**说明:**
>
>- 单词是指由非空格字符组成的字符序列。
>- 每个单词的长度大于 0，小于等于 *maxWidth*。
>- 输入单词数组 `words` 至少包含一个单词。
>- **示例:**
>
>```
>输入:
>words = ["This", "is", "an", "example", "of", "text", "justification."]
>maxWidth = 16
>输出:
>[
>   "This    is    an",
>   "example  of text",
>   "justification.  "
>]
>```

### 读题

1. 每一行的最大长度限定为 maxWidth，要求文本左右两端对齐。
2. 每一行每一个非结尾单词，都得后跟一个空格。
3. 每一行不够 maxWidth时，就得在单词之间加空格补充。
4. 如果某一行单词总长度不够 maxWidth，但是又无法均分到单词间隔时，多余的空格就优先从这一行的左往右分布。要求均匀。
5. 文本最后一行左对齐，不够 maxWidth 就尾部补齐。

### 解题思路(c++贪心详细题解3种情况)

其实读完这个题，基本上就有个朦胧的思路了。大体区分 3 种情况

1. 当前是最后一行，就左对齐，小于 maxWidth 的话就尾部补齐空格。
2. 当前行只有一个单词，左对齐，小于 maxWidth 的话就尾部补齐空格。
3. 当前行不止一个单词，也不是最后一行。那就得计算是否需要插入空格补位，如果要就得往里面插入。

这个思路还是比较直观的。而且第1、2种情况相对来说就比较好处理，可能第3种情况相对来说处理起来有点麻烦。

麻烦点在于我们读题后知道的：如果某一行单词总长度不够 maxWidth，但是又无法均分到单词间隔时，多余的空格就优先从这一行的左往右分布。要求均匀。

按照这个规则，我们想知道一行都有几个单词间隔（坑位）都各自需要补充多个空格。就得计算每一行已经有多少个单词 eWordsCount ，eWordsCount - 1就是坑位数，maxWidth - 每一行的单词总长 eLineLen 度就等于总共需要补充的空格数 eBlankCount 。然后我们就可以计算每个坑位最少需要填补的空格个数 eAvg，即 `eAvg = eBlankCount / (eWordsCount - 1);    `，但是可能会不够均分，按照题意就从左往右补，总共补 `extraBlank = eBlankCount % (eWordsCount - 1);`。

情况3举个例子吧：

<img src="https://tva1.sinaimg.cn/large/008i3skNly1guau8o7wkhj60pm0mc0uk02.jpg" alt="截屏2021-09-10 上午12.24.11" style="zoom:50%;" />

### 代码（带详细注释）

```c++
class Solution {
public:
    vector<string> fullJustify(vector<string> &words, int maxWidth) {
        // 结果
        vector<string> res;
        int size = words.size();
        // 每一行开始和结束
        int eLeft = 0, eRight = 0;
        // 记录每一行单词长度和
        int eLineLen = 0;
        // 当前行单词个数
        int eWordsCount = 0;
        // 当前行需要补缺的长度
        int eBlankCount = 0;
        // 当前行需要补空格的最少个数
        int eAvg = 0;
        // 当前行补完两个单词简的坑位后还多余的 空格个数
        int extraBlank = 0;
        while(1) {
            eLeft = eRight;
            eLineLen = 0;
            
            // 判断：当前行已有长度 + 下一个单词的长度 + 每个单词后需要加多的空格数 <= 20
            while (eRight < size && eLineLen + words[eRight].length() + eRight - eLeft <= maxWidth) {
                eLineLen += words[eRight++].length();
            }

            // 情况1：当前行是最后一行
            if (eRight == size) {
                // 单词左对齐，且单词之间应只有一个空格
                string s = words[eLeft];
                for (int i = eLeft + 1; i < size; i++) {
                    s += ' ' + words[i];
                }
                //在行尾补充剩余空格
                res.push_back(s + string(maxWidth - s.length(), ' '));
                return res;
            }

            // 当前行单词个数
            eWordsCount = eRight - eLeft;

            // 当前行需要补缺的长度
            eBlankCount = maxWidth - eLineLen;

            // 情况2：当前行只有一个单词
            if (eWordsCount == 1) {
                // 单词左对齐，在行尾补充剩余空格
                res.push_back(words[eLeft] + string(eBlankCount, ' '));
                continue;
            }

            /* 情况3：当前行不只一个单词
             首先计算每一行两个单词之间 缺失的最少空格数，因为可能会多余出来，比如一行一共缺8个空格，但是有3个单词间隔，就不能平均分，按照题意要尽量均匀，左边优先。
             此处 我是把每个单词后应该有的空格和 补缺空格一并处理，没有做区分。
             */
            eAvg = eBlankCount / (eWordsCount - 1); 
            extraBlank = eBlankCount % (eWordsCount - 1);

            string s1 = "";
            for (int i = eLeft; i < eRight; i++) {
                if (i == eRight - 1) {
                    s1 += words[i];
                }else {
                    if (i - eLeft < extraBlank) {
                        s1 += words[i] + string(eAvg + 1, ' ');
                    }else {
                        s1 += words[i] + string(eAvg, ' ');
                    }
                }
            }
            res.push_back(s1);
        }
        return res;
    }
};
```

