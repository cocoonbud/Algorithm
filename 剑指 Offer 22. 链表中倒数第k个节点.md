>输入一个链表，输出该链表中倒数第k个节点。为了符合大多数人的习惯，本题从1开始计数，即链表的尾节点是倒数第1个节点。
>
>例如，一个链表有 6 个节点，从头节点开始，它们的值依次是 1、2、3、4、5、6。这个链表的倒数第 3 个节点是值为 4 的节点。
>
>来源：[力扣（LeetCode）](https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof)
>
>**示例：**
>
>```
>给定一个链表: 1->2->3->4->5, 和 k = 2.
>
>返回链表 4->5.
>```

***

#### 前言

此题之前刷剑指 offer和 leetcode 时都有做过，leetcode 难度 easy 。今日每日一题再次遇到，旧题新做。

最近尽量抽工作之余时间，对每日的 leetcode 题做一个详细题解，多种解法。就像费曼学习法说的，在理解后要能输出出来，以教促学。今天会有5中解法：普通的两次遍历、双指针、递归、栈、哈希表。发散下思维，对于 easy 的题重拳出击！欢迎点赞评论收藏👍🏻

#### 解法1：

1. 先统计链表长度。倒数第 k 个节点 = 链表长度 - k。
2. 然后从头开始走，控制条件为链表长度 > k。此时走到的节点即所求节点。

此题简单，还是不明白的，动手在纸上一画就懂。

**### 代码**

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* getKthFromEnd(ListNode* head, int k) {
        ListNode *temp = head;
        int length = 0;
        while(temp != NULL) {
            length++;
            temp = temp->next;
        }
        temp = head;
        while (length > k) {
            temp = temp->next;  
            length--;
        }
        return temp;
    }
};
```

#### 解法2：双指针

1. 声明两个指针。快的 fast 先走 k 步。
2. 然后另外一个 slow 从头开始走，同时 fast 也继续走。步长1
3. 直到 fast 为 nullpt，此时 slow 指向的节点就是所求的倒数第 k 个节点。

<img src="https://tva1.sinaimg.cn/large/008i3skNgy1gu1mnzf88yj60rw0p8go902.jpg" alt="截屏2021-09-02 上午12.27.30.png" style="zoom:67%;" />

```c++
class Solution {
public:
    ListNode* getKthFromEnd(ListNode* head, int k) {
        //双指针
        ListNode *fast = head;
        ListNode *slow = head;
        while (k > 0) {
            fast = fast->next;
            k--;
        }
        while (fast != NULL) {
            fast = fast->next;
            slow = slow->next;
        }
        return slow;
    }
};
```

#### 解法3：递归

这个递归的过程就想象成一个震荡波，一圈一圈震荡出去到链表遇到NULL。然后回荡，在回荡的同时有一个变量来记录回荡了多少圈。回荡次数等于 k 的时候，当前节点就是所求节点。

```c++
class Solution {
public:
    ListNode* getKthFromEnd(ListNode* head, int k) {
        if(head == NULL) return NULL;

        ListNode *temp = getKthFromEnd(head->next, k);
        if(temp) return temp;
        cr++;
        if(cr == k) return head;
        return nullptr;
    }
private:
int cr = 0 ;
};
```

#### 解法4：栈

先全部压栈，再弹出 k - 1 个节点，此时栈顶节点即所求节点。

```c++
class Solution {
public:
    ListNode* getKthFromEnd(ListNode* head, int k) {
        stack<ListNode*> temp;
        while(head) {
            temp.push(head);
            head = head->next;
        }
        while(k > 1) {
            temp.pop();
            k--;
        }
        return temp.top();
    }
};
```

#### 解法5：哈希表

```c++
class Solution {
public:
    ListNode* getKthFromEnd(ListNode* head, int k) {
        unordered_map<int, ListNode *> hash;
        int length = 0;
        ListNode *temp = head;
        while(temp) {
            hash[length] = temp;
            temp = temp->next;
            length++;
        }
        unordered_map<int, ListNode*>::iterator it;
        it = hash.find(length - k);
        return it->second;
    }
};
```

#### 其他

[其他算法详细题解](https://github.com/cocoonbud/Algorithm)

