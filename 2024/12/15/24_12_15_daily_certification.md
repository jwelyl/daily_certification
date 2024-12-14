# 24_12_15_daily_certification

```
[#350 koreii] 데일리인증 20241215
1. 코딩 테스트 대비 알고리즘 학습
- DP (LeetCode 5. Longest Palindromic Substring)
- Sorting, Singly Linked List (LeetCode 147. Insertion Sort List)
```

# Problem Solving (Algorithm & SQL)

### LeetCode **Longest Palindromic Substring**

[Longest Palindromic Substring - LeetCode](https://leetcode.com/problems/longest-palindromic-substring/)

```java
class Solution {
    public String longestPalindrome(String s) {
        int[][] dp = new int[s.length()][s.length()];

        int maxLen = 0;
        int maxLenStart = -1;
        int maxLenEnd = -1;

        for(int k = 0; k < s.length(); k++) {
            for(int y = 0; y < s.length() - k; y++) {
                int x = y + k;

                if(x == y)
                    dp[y][x] = 1;
                else if(x - y == 1)
                    dp[y][x] = s.charAt(x) == s.charAt(y) ? 2 : 0;
                else
                    dp[y][x] = (s.charAt(x) == s.charAt(y) && dp[y + 1][x - 1] != 0) ?
                        dp[y + 1][x - 1] + 2 : 0;

                if(maxLen < dp[y][x]) {
                    maxLen = dp[y][x];
                    maxLenStart = y;
                    maxLenEnd = x + 1;
                }
            }
        }

        return s.substring(maxLenStart, maxLenEnd);
    }
}
```

### LeetCode **Insertion Sort List**

[Insertion Sort List - LeetCode](https://leetcode.com/problems/insertion-sort-list/description/)

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
    public ListNode insertionSortList(ListNode head) {
        ListNode dummy = new ListNode(-5_001);
        dummy.next = head;
        head = dummy;

        ListNode prev = head.next;
        ListNode cur = prev.next;

        while(cur != null) {
            ListNode next = cur.next;
            ListNode sprev = head;
            ListNode scur = head.next;

            boolean swapped = false;

            while(scur != cur) {
                if(cur.val <= scur.val) {
                    swapped = true;
                    break;
                }

                sprev = scur;
                scur = scur.next;
            }

            if(swapped) {
                prev.next = next;
                sprev.next = cur;
                cur.next = scur;
                cur = next;
            }
            else {
                prev = cur;
                cur = next;
            }
        }

        return head.next;
    }
}
```