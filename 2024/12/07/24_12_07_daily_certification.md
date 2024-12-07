# 24_12_07_daily_certification

### Deadlock Detection and Recovery

![image.png](24_12_07_daily_certification%20154154b2a3b880f097f3cd7c8b1999e0/image.png)

자원 당 인스턴스가 1개일 경우

![image.png](24_12_07_daily_certification%20154154b2a3b880f097f3cd7c8b1999e0/image%201.png)

![image.png](24_12_07_daily_certification%20154154b2a3b880f097f3cd7c8b1999e0/image%202.png)

Cycle Detection Time Complexity

N Processes → O(N^2) (O(E) = O(N^2))

모든 Edge를 다 따라가봐야 하므로

자원 당 인스턴스가 2개 이상일 경우

![image.png](24_12_07_daily_certification%20154154b2a3b880f097f3cd7c8b1999e0/image%203.png)

Banker’s Algorithm과 다르게 Request는 현재 요청량임 (최대 요청량이 아님)

낙관적으로 보기

![image.png](24_12_07_daily_certification%20154154b2a3b880f097f3cd7c8b1999e0/image%204.png)

Recovery

![image.png](24_12_07_daily_certification%20154154b2a3b880f097f3cd7c8b1999e0/image%205.png)

Termination

- 데드락 걸린 프로세스 모두 죽이기
- 프로세스 하나씩 죽여보기 (데드락 풀릴때까지)

Preemption

- 데드락 걸린 프로세스 중 하나를 victim으로 선정하여 자원 강탈함
- 하나한테서만 계속 자원을 뺏지 않도록 고려해야함

### Deadlock Ignorance

![image.png](24_12_07_daily_certification%20154154b2a3b880f097f3cd7c8b1999e0/image%206.png)

# Problem Solving (Algorithm & SQL)

### LeetCode **Largest Number**

[Largest Number - LeetCode](https://leetcode.com/problems/largest-number/description/)

```java
import java.util.Comparator;

class Solution {
    public String largestNumber(int[] nums) {
        StringBuilder sb = new StringBuilder();
        String[] numStrs = new String[nums.length];

        for(int i = 0; i < nums.length; i++)
            numStrs[i] = Integer.toString(nums[i]);
        
        Arrays.sort(numStrs, new Comparator<String>() {
            @Override
            public int compare(String str1, String str2) {
                String lr = str1 + str2;
                String rl = str2 + str1;
                
                if(lr.compareTo(rl) < 0)   //  lr이 rl보다 큰 경우 ex) str1 = 3, str2 = 30 -> lr = 330, rl = 303
                    return 1;  //  str1이 먼저 나와야 함
                else
                    return -1;   //  str2가 먼저 나와야 함
            }
        });

        for(String num : numStrs)
            sb.append(num);

        String answer = sb.toString();

        boolean all0 = true;
        for(int i = 0; i < answer.length(); i++) {
            if(answer.charAt(i) != '0') {
                all0 = false;
                break;
            }
        }

        return all0 ? "0" : answer;
    }
}
```

### LeetCode **Balanced Binary Tree**

[Balanced Binary Tree - LeetCode](https://leetcode.com/problems/balanced-binary-tree/description/)

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
    private boolean balanced = true;

    public boolean isBalanced(TreeNode root) {
        dfs(root);

        return balanced;
    }

    //  root의 높이 반환
    private int dfs(TreeNode root) {
        if(root == null)    //  노드가 없을 경우 높이 -1로 간주
            return -1;

        int lh = dfs(root.left);
        int rh = dfs(root.right);

        if(Math.abs(lh - rh) > 1)
            balanced = false;

        return Math.max(lh, rh) + 1;
    }
}
```

### LeetCode **Best Time to Buy and Sell Stock**

[Best Time to Buy and Sell Stock - LeetCode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/)

```java
class Solution {
    public int maxProfit(int[] prices) {
        int maxProfit = 0;  //  최대 이익

        Deque<Integer> deque = new LinkedList<>();

        for(int i = 0; i < prices.length; i++) {
            while(!deque.isEmpty() && deque.peekLast() > prices[i])
                deque.pollLast();

            deque.offerLast(prices[i]);
            maxProfit = Math.max(maxProfit, deque.peekLast() - deque.peekFirst());
        }

        return maxProfit;
    }
}
```

### LeetCode Group Anagrams

[Group Anagrams - LeetCode](https://leetcode.com/problems/group-anagrams/description/)

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        List<List<String>> answer = new ArrayList<>();
        
        MyString[] myStrs = new MyString[strs.length];
        for(int i = 0; i < strs.length; i++)
            myStrs[i] = new MyString(strs[i]);

        Arrays.sort(myStrs);

        String before = "X";
        List<String> group = null;

        for(int i = 0; i < strs.length; i++) {
            MyString myStr = myStrs[i];

            if(!myStr.sorted.equals(before)) {
                if(group != null)
                    answer.add(group);
                
                group = new ArrayList<>();
                before = myStr.sorted;
                group.add(myStr.toString());
            }
            else group.add(myStr.toString());
        }

        answer.add(group);

        return answer;
    }

    private String sortedString(String str) {
        char[] arr = str.toCharArray();
        Arrays.sort(arr);

        return new String(arr);
    }
}

class MyString implements Comparable<MyString> {
    public String orig;
    public String sorted;

    public MyString(String orig) {
        this.orig = orig;
        char[] arr = orig.toCharArray();
        Arrays.sort(arr);

        this.sorted = new String(arr);
    }

    @Override
    public int compareTo(MyString other) {
        return this.sorted.compareTo(other.sorted);
    }

    @Override
    public String toString() {
        return this.orig;
    }
}
```