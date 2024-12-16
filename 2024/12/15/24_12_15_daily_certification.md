# 24_12_15_daily_certification

![image.png](24_12_15_daily_certification%2015c154b2a3b881569544e512250a416d/image.png)

![image.png](24_12_15_daily_certification%2015c154b2a3b881569544e512250a416d/image%201.png)

각 sector는 logical block data(512 bytes) + 부가적인 데이터(Error Correcting Code (hash function 같은 느낌), address mapping을 위한 sector number) (header, trailer)

Booting

- CPU는 Memory에만 접근 가능, HDD에는 접근 부분
- 메모리의 ROM (non-volatile)에 있는 small bootstrap loader를 실행

![image.png](24_12_15_daily_certification%2015c154b2a3b881569544e512250a416d/image%202.png)

Access Time의 대부분은 Seek Time임, Transfer Time은 미미함

![image.png](24_12_15_daily_certification%2015c154b2a3b881569544e512250a416d/image%203.png)

![image.png](24_12_15_daily_certification%2015c154b2a3b881569544e512250a416d/image%204.png)

![image.png](24_12_15_daily_certification%2015c154b2a3b881569544e512250a416d/image%205.png)

현재 head 위치에서 가장 가까운 요청을 먼저 처리

![image.png](24_12_15_daily_certification%2015c154b2a3b881569544e512250a416d/image%206.png)

![image.png](24_12_15_daily_certification%2015c154b2a3b881569544e512250a416d/image%207.png)

![image.png](24_12_15_daily_certification%2015c154b2a3b881569544e512250a416d/image%208.png)

Circular SCAN

![image.png](24_12_15_daily_certification%2015c154b2a3b881569544e512250a416d/image%209.png)

![image.png](24_12_15_daily_certification%2015c154b2a3b881569544e512250a416d/image%2010.png)

![image.png](24_12_15_daily_certification%2015c154b2a3b881569544e512250a416d/image%2011.png)

![image.png](24_12_15_daily_certification%2015c154b2a3b881569544e512250a416d/image%2012.png)

![image.png](24_12_15_daily_certification%2015c154b2a3b881569544e512250a416d/image%2013.png)

File System : 512 byte의 Sector 단위 (공간 효율성 중시)

Swap Area : Physical Memory의 연장선상, 빠른 read/write 필요, seek time 줄여야 함, 속도 효율성 512KB 단위 대용량으로 올리고 내림. 어쨋든 더 큰 단위로 올리고 내린다는게 중요

![image.png](24_12_15_daily_certification%2015c154b2a3b881569544e512250a416d/image%2014.png)

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

### LeetCode **Longest Univalue Path**

[Longest Univalue Path - LeetCode](https://leetcode.com/problems/longest-univalue-path/description/)

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    private int maxLength = 0;

    public int longestUnivaluePath(TreeNode root) {
        dfs(root);

        return maxLength;
    }

    private int[] dfs(TreeNode root) {
        if(root == null)
            return null;
        
        int[] res = {0, 0};

        if(root.left != null) {
            int[] left = dfs(root.left);

            if(root.val == root.left.val)
                res[0] = Math.max(left[0], left[1]) + 1;
        }
        if(root.right != null) {
            int[] right = dfs(root.right);

            if(root.val == root.right.val)
                res[1] = Math.max(right[0], right[1]) + 1;
        }

        maxLength = Math.max(maxLength, res[0] + res[1]);

        return res;
    }
}
```