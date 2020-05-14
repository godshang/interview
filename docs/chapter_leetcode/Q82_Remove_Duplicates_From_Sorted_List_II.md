# 82. 删除排序链表中的重复元素 II

给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中 没有重复出现 的数字。

**示例 1:**

```
输入: 1->2->3->3->4->4->5
输出: 1->2->5
```

**示例 2:**

```
输入: 1->1->1->2->3
输出: 2->3
```

## 方法一：单指针遍历

1. 初始化：因为本题要求删除所有出现重复的结点，所以原头结点也可能会被删除，需要引入dummyhead作为新的头结点，并令工作指针p指向dummyhead。
2. 遍历过程：由于链表是排好序的，所以我们始终检查p -> next和p -> next -> next这两个位置的值：
3. 如果两者不相等，说明p -> next的值一定没有重复，p后移一位
4. 如果二者相等，我们后移p -> next -> next指针以获得一个与p -> next的值不相等的新结点，让p -> next指向这个新结点（即跳过了p和其后面重复的一段结点），p本身不作移动，直接进入下一轮遍历。
5. 返回值：当p后面无结点或只有一个结点时，遍历结束，此时dummyhead -> next为处理后的链表的头结点。

```java
public class Solution {

    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) return null;
        ListNode dummy = new ListNode(), p = dummy;
        dummy.next = head;
        while (p.next != null && p.next.next != null) {
            boolean duplicated = false;
            while (p.next.next != null && p.next.val == p.next.next.val) {
                duplicated = true;
                p.next.next = p.next.next.next;
            }
            if (duplicated) {
                p.next = p.next.next;
            } else {
                p = p.next;
            }
        }
        return dummy.next;
    }
}
```



>作者：newpp
> 
>链接：https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/solution/dummyheadjia-dan-zhi-zhen-bian-li-di-gui-liang-cho/
> 
>来源：力扣（LeetCode）
> 
>著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。