>给定一个 n 个元素有序的（升序）整型数组 nums 和一个目标值 target  ，写一个函数搜索 nums 中的 target，如果目标值存在返回下标，否则返回 -1。
>
>来源：[力扣（LeetCode）](https://leetcode-cn.com/problems/binary-search)
>
>**示例 1:**
>
>```
>输入: nums = [-1,0,3,5,9,12], target = 9
>输出: 4
>解释: 9 出现在 nums 中并且下标为 4
>```

***

#### 读题

1. 升序有序数组

2. 数组中无重复元素（若有重复元素，使用二分查找法返回的元素下标就可能不唯一）

要求：写个函数去搜索 给定数组中是否有给定的 target。有就返回下标，没有返回-1。

#### 解题思路

二分查找基本思想：包含 n 个元素的升序的数组 nums，将 n 个元素分成个数大致相同的两半，取 nums[n/2]与target相比较。

1. 若 nums[n/2] 等于 target，算法终止。

2. 若 nums[n/2] > target，则继续在 nums 的左半部继续搜索。

3. 若 nums[n/2] < target，则继续在 nums 的右半部继续搜索。

看着挺简单吧？这里主要的问题就是边界的处理，也就是第2、3步的左半部、右半部怎么处理。

##### 方法1：

定义一个在左闭右闭的区间里，也就是[left, right]，left 就是 nums 的第一个元素 index，right 是 nums 的最后一个元素index，我们就在这个区间里去找 target。

以题目示例1： nums = [-1,0,3,5,9,12], target = 9 为例。



![截屏2021-09-06 上午12.43.13.png](https://pic.leetcode-cn.com/1630861770-SIaOLC-%E6%88%AA%E5%B1%8F2021-09-06%20%E4%B8%8A%E5%8D%8812.43.13.png)



##### 方法1代码

```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1, mid = 0, num = 0;
        while (left <= right) {
	          //这么写是防止溢出，等同于(left + right) / 2, 也可写为left + ((right - left) >> 1)
            mid = left + (right - left) / 2; 
            num = nums[mid];
            if (num == target) {
                return mid;
            }else if (num < target) {
                left = mid + 1;
            }else {
                right = mid - 1;
            }
        }
        return -1;
    }
};
```

##### 方法2

也会有另外一种写法，定义在 [left, right) 这个区间去搜 target。这样定义的话，代码大体上还是一样的，边界处理就不同了。

方法1的 while (left <= right) 变为 while (left < right) ，因为定义的左闭右开区间就不包含right，left == right 就没意义了。

##### 方法2代码

```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0, right = nums.size(), mid = 0, num = 0;
        while (left < right) {
		    mid = left + (right - left) / 2;
		    num = nums[mid];
            if (num == target) {
				return mid; 
            } else if (num < target) {
                left = mid + 1; 
            } else { 
                right = mid; 
            }
        }
        return -1;
    }
};
```

都看到这了，如果感觉题解对你有点帮助，👍🏻一个支持下吧！