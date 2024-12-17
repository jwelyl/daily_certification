# 24_12_17_daily_certification

### LeetCode **Merge k Sorted Lists**

[Merge k Sorted Lists - LeetCode](https://leetcode.com/problems/merge-k-sorted-lists/description/)

**0번 리스트부터 그 다음 리스트랑 하나씩 합하기**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        ListNode prev = lists.length == 0 ? null : lists[0];

        for(int i = 1; i < lists.length; i++) {
            ListNode cur = lists[i];

            ListNode head = new ListNode(Integer.MIN_VALUE);
            ListNode pos = head;

            while(prev != null && cur != null) {
                if(prev.val <= cur.val) {
                    pos.next = prev;
                    pos = prev;
                    prev = prev.next;
                    pos.next = null;
                }
                else {
                    pos.next = cur;
                    pos = cur;
                    cur = cur.next;
                    pos.next = null;
                }
            }

            while(prev != null) {
                pos.next = prev;
                pos = prev;
                prev = prev.next;
                pos.next = null;
            }

            while(cur != null) {
                pos.next = cur;
                pos = cur;
                cur = cur.next;
                pos.next = null;
            }

            prev = head.next;
        }

        return prev;
    }
}
```

**Priority Queue** 

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        PriorityQueue<ListNode> pq = new PriorityQueue<>((node1, node2) -> Integer.compare(node1.val, node2.val));

        ListNode head = new ListNode(Integer.MIN_VALUE);
        ListNode pos = head;

        for(int k = 0; k < lists.length; k++) {
            ListNode list = lists[k];
            ListNode cur = list;

            while(cur != null) {
                ListNode next = cur.next;
                cur.next = null;
                pq.offer(cur);
                cur = next;
            }
        }

        while(!pq.isEmpty()) {
            ListNode node = pq.poll();
            pos.next = node;
            pos = node;
        }

        return head.next;
    }
}
```

**Divide And Conquer, Merge Sort**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if(lists.length == 0)
            return null;

        return mergeSort(lists, 0, lists.length - 1);
    }

    private ListNode mergeSort(ListNode[] lists, int start, int end) {
        if(start == end)
            return lists[start];

        ListNode left = mergeSort(lists, start, (start + end) / 2);
        ListNode right = mergeSort(lists, (start + end) / 2 + 1, end);

        return merge(left, right);
    }

    private ListNode merge(ListNode list1, ListNode list2) {
        ListNode head = new ListNode(Integer.MIN_VALUE);
        ListNode pos = head;

        while(list1 != null && list2 != null) {
            if(list1.val < list2.val) {
                pos.next = list1;
                pos = list1;
                list1 = list1.next;
                pos.next = null;
            }
            else {
                pos.next = list2;
                pos = list2;
                list2 = list2.next;
                pos.next = null;
            }
        }

        while(list1 != null) {
            pos.next = list1;
            pos = list1;
            list1 = list1.next;
            pos.next = null;
        }

        while(list2 != null) {
            pos.next = list2;
            pos = list2;
            list2 = list2.next;
            pos.next = null;
        }

        return head.next;
    }
}
```