# 23. 合并K个排序链表

合并 k 个排序链表，返回合并后的排序链表。请分析和描述算法的复杂度。

**示例:**

```
输入:
[
  1->4->5,
  1->3->4,
  2->6
]
输出: 1->1->2->3->4->4->5->6
```

## 方法一：顺序合并

**思路**

我们可以想到一种最朴素的方法：用一个变量 ans 来维护以及合并的链表，第 ii 次循环把第 ii 个链表和 ans 合并，答案保存到 ans 中。

**代码**

```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode *a, ListNode *b) {
        if ((!a) || (!b)) return a ? a : b;
        ListNode head, *tail = &head, *aPtr = a, *bPtr = b;
        while (aPtr && bPtr) {
            if (aPtr->val < bPtr->val) {
                tail->next = aPtr; aPtr = aPtr->next;
            } else {
                tail->next = bPtr; bPtr = bPtr->next;
            }
            tail = tail->next;
        }
        tail->next = (aPtr ? aPtr : bPtr);
        return head.next;
    }

    ListNode* mergeKLists(vector<ListNode*>& lists) {
        ListNode *ans = nullptr;
        for (size_t i = 0; i < lists.size(); ++i) {
            ans = mergeTwoLists(ans, lists[i]);
        }
        return ans;
    }
};
```

**复杂度**

* 时间复杂度：假设每个链表的最长长度是 n。在第一次合并后，ans 的长度为 n；第二次合并后，ans 的长度为 $2\times n$ ，第 i 次合并后，ans 的长度为 $i\times n$。第 i 次合并的时间代价是 $O(n + (i - 1) \times n) = O(i \times n)$，那么总的时间代价为 $O(\sum_{i = 1}^{k} (i \times n)) = O(\frac{(1 + k)\cdot k}{2} \times n) = O(k^2 n)$，故渐进时间复杂度为 $O(k^2 n)$。
* 空间复杂度：没有用到与 k 和 n 规模相关的辅助空间，故渐进空间复杂度为 O(1)。

## 方法二：分治合并

**思路**

考虑优化方法一，用分治的方法进行合并。

* 将 k 个链表配对并将同一对中的链表合并；
* 第一轮合并以后， k 个链表被合并成了 $\frac{k}{2} $ 个链表，平均长度为 $ \frac{2n}{k} $ ，然后是 $ \frac{k}{4} $个链表，$ \frac{k}{8} $ 个链表等等；
* 重复这一过程，直到我们得到了最终的有序链表。

<img src="https://pic.leetcode-cn.com/6f70a6649d2192cf32af68500915d84b476aa34ec899f98766c038fc9cc54662-image.png" />

**代码**

```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode *a, ListNode *b) {
        if ((!a) || (!b)) return a ? a : b;
        ListNode head, *tail = &head, *aPtr = a, *bPtr = b;
        while (aPtr && bPtr) {
            if (aPtr->val < bPtr->val) {
                tail->next = aPtr; aPtr = aPtr->next;
            } else {
                tail->next = bPtr; bPtr = bPtr->next;
            }
            tail = tail->next;
        }
        tail->next = (aPtr ? aPtr : bPtr);
        return head.next;
    }

    ListNode* merge(vector <ListNode*> &lists, int l, int r) {
        if (l == r) return lists[l];
        if (l > r) return nullptr;
        int mid = (l + r) >> 1;
        return mergeTwoLists(merge(lists, l, mid), merge(lists, mid + 1, r));
    }

    ListNode* mergeKLists(vector<ListNode*>& lists) {
        return merge(lists, 0, lists.size() - 1);
    }
};
```

复杂度

* 时间复杂度：考虑递归「向上回升」的过程——第一轮合并 $ \frac{k}{2} $ 组链表，每一组的时间代价是 O(2n)；第二轮合并 $ \frac{k}{4} $组链表，每一组的时间代价是 O(4n)......所以总的时间代价是 $ O(\sum_{i = 1}^{\infty} \frac{k}{2^i} \times 2^i n) = O(kn \times \log k) $，故渐进时间复杂度为 $ O(kn \times \log k)$ 。
* 空间复杂度：递归会使用到 $ O(\log k) $ 空间代价的栈空间。

## 方法三：使用优先队列合并

**思路**

这个方法和前两种方法的思路有所不同，我们需要维护当前每个链表没有被合并的元素的最前面一个，kk 个链表就最多有 kk 个满足这样条件的元素，每次在这些元素里面选取 val 属性最小的元素合并到答案中。在选取最小元素的时候，我们可以用优先队列来优化这个过程。

**代码**

```cpp
class Solution {
public:
    struct Status {
        int val;
        ListNode *ptr;
        bool operator < (const Status &rhs) const {
            return val > rhs.val;
        }
    };

    priority_queue <Status> q;

    ListNode* mergeKLists(vector<ListNode*>& lists) {
        for (auto node: lists) {
            if (node) q.push({node->val, node});
        }
        ListNode head, *tail = &head;
        while (!q.empty()) {
            auto f = q.top(); q.pop();
            tail->next = f.ptr; 
            tail = tail->next;
            if (f.ptr->next) q.push({f.ptr->next->val, f.ptr->next});
        }
        return head.next;
    }
};
```

**复杂度**

* 时间复杂度：考虑优先队列中的元素不超过 k 个，那么插入和删除的时间代价为 $ O(\log k) $，这里最多有 kn 个点，对于每个点都被插入删除各一次，故总的时间代价即渐进时间复杂度为 $ O(kn \times \log k) $。
* 空间复杂度：这里用了优先队列，优先队列中的元素不超过 k 个，故渐进空间复杂度为 $ O(k) $。



>作者：LeetCode-Solution
>链接：https://leetcode-cn.com/problems/merge-k-sorted-lists/solution/he-bing-kge-pai-xu-lian-biao-by-leetcode-solutio-2/
>来源：力扣（LeetCode）
>著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
