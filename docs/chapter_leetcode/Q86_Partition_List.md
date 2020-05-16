# 86. 分隔链表

给定一个链表和一个特定值 x，对链表进行分隔，使得所有小于 x 的节点都在大于或等于 x 的节点之前。

你应当保留两个分区中每个节点的初始相对位置。

**示例:**

```
输入: head = 1->4->3->2->5->2, x = 3
输出: 1->2->2->4->3->5
```

## 双指针法

**直觉**

我们可以用两个指针before 和 after 来追踪上述的两个链表。两个指针可以用于分别创建两个链表，然后将这两个链表连接即可获得所需的链表。

**算法**

1. 初始化两个指针 before 和 after。在实现中，我们将两个指针初始化为哑 ListNode。这有助于减少条件判断。（不信的话，你可以试着写一个不带哑结点的方法自己看看！）

<img src="https://pic.leetcode-cn.com/c688c95bea37ba6d146f5488544cf775f27a6275baf06a4e0721971931701de4-image.png" />

2. 利用head指针遍历原链表。
3. 若head 指针指向的元素值 小于 x，该节点应当是 before 链表的一部分。因此我们将其移到 before 中。

<img src="https://pic.leetcode-cn.com/dcca771242fd52f47eec9d46a7a9f28e14f12aa3f7812a7e4ed10bec699fd45c-image.png" />

4. 否则，该节点应当是after 链表的一部分。因此我们将其移到 after 中。

<img src="https://pic.leetcode-cn.com/e3ba113ee4b09ec646723077ae35774e077262d4596c122dbd26a5f6090c77ef-image.png" />

5. 遍历完原有链表的全部元素之后，我们得到了两个链表 before 和 after。原有链表的元素或者在before 中或者在 after 中，这取决于它们的值。

<img src="https://pic.leetcode-cn.com/f1c7797cce53e0a2334af83bfd207c84e1195db4b74a962253c49e3798b4eb45-image.png" />

6. 现在，可以将 before 和 after 连接，组成所求的链表。

<img src="https://pic.leetcode-cn.com/f1bb41e5ae3a34bed722b5a703fb4a81474850781da1597617cd52ce5d3676e4-image.png" />

为了算法实现更容易，我们使用了哑结点初始化。不能让哑结点成为返回链表中的一部分，因此在组合两个链表时需要向前移动一个节点。

```java
class Solution {
    public ListNode partition(ListNode head, int x) {

        // before and after are the two pointers used to create the two list
        // before_head and after_head are used to save the heads of the two lists.
        // All of these are initialized with the dummy nodes created.
        ListNode before_head = new ListNode(0);
        ListNode before = before_head;
        ListNode after_head = new ListNode(0);
        ListNode after = after_head;

        while (head != null) {

            // If the original list node is lesser than the given x,
            // assign it to the before list.
            if (head.val < x) {
                before.next = head;
                before = before.next;
            } else {
                // If the original list node is greater or equal to the given x,
                // assign it to the after list.
                after.next = head;
                after = after.next;
            }

            // move ahead in the original list
            head = head.next;
        }

        // Last node of "after" list would also be ending node of the reformed list
        after.next = null;

        // Once all the nodes are correctly assigned to the two lists,
        // combine them to form a single list which would be returned.
        before.next = after_head.next;

        return before_head.next;
    }
}
```


>作者：LeetCode
> 
>链接：https://leetcode-cn.com/problems/partition-list/solution/fen-ge-lian-biao-by-leetcode/
> 
>来源：力扣（LeetCode）
> 
>著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。