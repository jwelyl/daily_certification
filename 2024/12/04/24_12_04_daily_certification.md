# 24_12_04_daily_certification

```
[#339 koreii] 데일리인증 20241204
1. 코딩 테스트 대비 알고리즘 학습
- Bruteforcing, Backtracking, Bit Manipulation (LeetCode 78. Subsets)
- Singly Linked List (LeetCode 234. Palindrome Linked List)
- Backtracking, Stack (LeetCode 39. Combination Sum)
- DFS, Binary Tree (LeetCode 543. Diameter of Binary Tree)
```

# Problem Solving (Algorithm & SQL)

### LeetCode **Subsets**

[Subsets - LeetCode](https://leetcode.com/problems/subsets/description/)

```java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> subsets = new ArrayList<>();

        for(int i = 0; i <= (1 << nums.length) - 1; i++) {
            List<Integer> subset = new ArrayList<>();

            int idx = 0;
            int num = i;
            
            while(num > 0) {
                int bit = (num & 1);

                if(bit == 1)
                    subset.add(nums[idx]);

                idx++;
                num >>= 1;
            }

            subsets.add(subset);
        }

        return subsets;
    }
}
```

### LeetCode **Palindrome Linked List**

[Palindrome Linked List - LeetCode](https://leetcode.com/problems/palindrome-linked-list/description/)

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
    public boolean isPalindrome(ListNode head) {
        int size = 0;

        ListNode cur = head;

        while(cur != null) {
            cur = cur.next;
            size++;
        }

        // System.out.println("size = " + size);

        ListNode reversed = new ListNode(-1);   //  더미노드

        for(int i = 0; i < size / 2; i++) {
            cur = head;

            head = head.next;
            cur.next = reversed.next;
            reversed.next = cur;
        }

        if(size % 2 == 1)   // 원본 sll의 길이가 홀수일 경우
            head = head.next;
        reversed = reversed.next;

        for(int i = 0; i < size / 2; i++) {
            if(head.val != reversed.val)
                return false;

            head = head.next;
            reversed = reversed.next;
        }

        return true;
    }
}
```

### LeetCode **39. Combination Sum**

[Combination Sum - LeetCode](https://leetcode.com/problems/combination-sum/description/)

```java
class Solution {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> combinations = new ArrayList<>();

        for(int start = 0; start < candidates.length; start++)
            comb(candidates, start, 0, target, new Stack<>(), combinations);

        return combinations;
    }

    private boolean comb(int[] candidates, int cur, int sum, int target, Stack<Integer> stack, List<List<Integer>> combinations) {
         boolean ret = false;    //  초과하면 false

        stack.push(candidates[cur]);    //  cur번째 수 추가

        if(sum + candidates[cur] == target) {   //  target을 만들어 낸 경우
            combinations.add(new ArrayList<>(stack));
            ret = true;
        }
        else if(sum + candidates[cur] < target) {   //  수를 더 뽑아야 할 경우
            for(int i = cur; i < candidates.length; i++) {
                if(comb(candidates, i, sum + candidates[cur], target, stack, combinations)) //  i번째 수를 추가해서 target을 만들 수 있을 경우
                    ret = true;
            }
        }

        stack.pop();    //  cur번째 수 제거
        return ret;
    }
}
```

### LeetCode **Diameter of Binary Tree**

[Diameter of Binary Tree - LeetCode](https://leetcode.com/problems/diameter-of-binary-tree/)

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
    private int answer = 0;

    public int diameterOfBinaryTree(TreeNode root) {
        dfs(root);

        return answer;
    }

    private int dfs(TreeNode root) {
        if(root == null)
            return 0;

        int left = dfs(root.left);
        int right = dfs(root.right);

        answer = Math.max(answer, left + right);
        return Math.max(left, right) + 1;
    }
}
```