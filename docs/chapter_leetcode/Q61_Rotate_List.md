# 61. 旋转链表

给定一个链表，旋转链表，将链表每个节点向右移动 k 个位置，其中 k 是非负数。

**示例 1:**

```
输入: 1->2->3->4->5->NULL, k = 2
输出: 4->5->1->2->3->NULL
解释:
向右旋转 1 步: 5->1->2->3->4->NULL
向右旋转 2 步: 4->5->1->2->3->NULL
```

**示例 2:**

```
输入: 0->1->2->NULL, k = 4
输出: 2->0->1->NULL
解释:
向右旋转 1 步: 2->0->1->NULL
向右旋转 2 步: 1->2->0->NULL
向右旋转 3 步: 0->1->2->NULL
向右旋转 4 步: 2->0->1->NULL
```

## 算法

依旧还是双指针，这种有间隔的链表，大多数都是双指针。

先让快指针走 k 个位置，然后两个指针一起走完整个链表。

<img src="https://pic.leetcode-cn.com/c1f1b1b26a22c2119c30f90c31d93cd5b241557f2d773430eb10e587a5ffb11f.jpg" />

这时，两个指针之间的区域就是我们要移动的区域，只要更改指针指向，就完事了。

* 即，first->next 指向 head，完成旋转（当然还没完事）；
* head 指向 second->next，头结点指向确认；
* second->next 指向空节点，尾结点指向确认；
* 打完收工。

<img src="https://pic.leetcode-cn.com/507bc9f5794a9310c58ca21572bca63d940a94f17aee39e5dc1c11b30e5a3de7.jpg" />

>作者：TeFuirnever
> 
>链接：https://leetcode-cn.com/problems/rotate-list/solution/shou-hui-man-hua-tu-jie-leetcodezhi-xuan-zhuan-lia/
> 
>来源：力扣（LeetCode）
> 
>著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。