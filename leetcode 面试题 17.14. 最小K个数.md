>设计一个算法，找出数组中最小的k个数。以任意顺序返回这k个数均可。
>
>示例：
>
>输入： arr = [1,3,5,7,2,4,6,8], k = 4
>输出： [1,2,3,4]
>提示：
>
>0 <= len(arr) <= 100000
>0 <= k <= min(100000, len(arr))
>
>来源：[力扣（LeetCode）](https://leetcode-cn.com/problems/smallest-k-lcci)

****

#### 解题思路
其实选用任意一种排序方法，然后取前k个即可。这里就选择快排。

1. 从数组中取出一个元素当做pivot。
2. partition，重排序数组。使得所有小于pivot的元素在它前面，所有大于pivot的元素在它后面。这样的划分以后，基准数的位置已经排好了。
3. 对小于pivot的子数组和大于pivot的子数组，递归步骤2。
4. 取排序好的数组前 k 个元素return。

啰嗦一句，快排的优点在in place swap，不需要额外空间。

#### 代码

```c++
class Solution {
public:
    vector<int> smallestK(vector<int>& arr, int k) {
        quickSort(arr, 0, arr.size() - 1);
        return vector<int>(arr.begin(), arr.begin() + k);
    }

    void quickSort(vector<int> & arr, int left, int right) {
        if(left < right) {
            //temp这里是基准数 i j两个哨兵
            int temp = arr[left], i = left, j = right, t = 0;
            
            while(i < j) {
                //寻找右边第一个小于基准值的下标
                while(arr[j] >= temp && i < j) {
                    j--;
                }
                //寻找左边第一个大于基准值的下标
                while(arr[i] <= temp && i < j) {
                    i++;
                }
                
                if (i < j) {
                    t = arr[i];
                    arr[i] = arr[j];
                    arr[j] = t;
                }
            }
            //两个哨兵相遇的时候把当前的数与基准数进行交换。
            arr[left] = arr[i];
            arr[i] = temp;
            //继续处理左边
            quickSort(arr, left, j-1);
            //继续处理右边
            quickSort(arr, j+1, right);
        }
    }
};
```

平均时间复杂度：O(nlogn)，最差时间复杂度：O(n^2)，最好时间复杂度：O(logn)，最差空间复杂度O(n)。

#### 其他

[有更快的快速选择法，可参考](https://zhuanlan.zhihu.com/p/64627590#:~:text=%E6%A6%82%E8%BF%B0,%E6%98%AF%E7%94%B1Tony%20Hoare%20%E5%8F%91%E6%98%8E%E3%80%82)