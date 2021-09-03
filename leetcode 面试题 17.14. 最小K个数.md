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

#### 前言

本题面上看就很简单了，选用任意一种排序方法，然后取前k个即可。

本文这里就以快速排序、大根堆两种解法为例吧。

#### 快速排序

1. 从数组中取出一个元素当做pivot。
2. partition，重排序数组。使得所有小于pivot的元素在它前面，所有大于pivot的元素在它后面。这样的划分以后，基准数的位置已经排好了。
3. 对小于pivot的子数组和大于pivot的子数组，递归步骤2。
4. 取排序好的数组前 k 个元素return。

啰嗦一句，快排的优点在in place swap，不需要额外空间。

##### 代码

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

#### 大根堆

大根堆：**父节点一定大于字节点，根节点一定是最大的。**

1. 首先往优先队列塞 arr 的前 k 个数
2. 然后从 arr 的 k 位置开始遍历
   - `arr[i] >= top`：arr[i] 不可能属于第 k 小数（已有 k 个元素在堆中） continue
   - `arr[i] < top`：arr[i] 可能属于第 k 小数，弹出堆顶元素，并放入 arr[i]。

处理完 arr 后，堆内剩余的就是所求的最小 k 个数。

##### 代码

```c++
class Solution {
public:
    vector<int> smallestK(vector<int>& arr, int k) {
        vector<int> res;

        if (k == 0) return res;
        
        priority_queue<int> q;

        for (int i = 0; i < k; i++) {
            q.push(arr[i]);
        }

        int size = arr.size();

        for (int i = k; i < arr.size(); i++) {
            if (arr[i] < q.top()) {
                q.pop();
                q.push(arr[i]);
            }
        }

        while(!q.empty()) {
            res.push_back(q.top());
            q.pop();
        }

        return res;
    }
};
```

时间复杂度为 O(n*logk)，空间复杂度O(k)

#### 其他

[快速选择法，可参考](https://zhuanlan.zhihu.com/p/64627590#:~:text=%E6%A6%82%E8%BF%B0,%E6%98%AF%E7%94%B1Tony%20Hoare%20%E5%8F%91%E6%98%8E%E3%80%82)

[其他算法详解](https://github.com/cocoonbud/Algorithm)