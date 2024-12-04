# 24_12_03_daily_certification

```
[#338 koreii] 데일리인증 20241203
1. Operating System
1-1. Synchronization Problems
	- Bounded-Buffer Problem (Producer-Consumer Problem)
	- Readers-Writers Problem
	- Dining Philosophers Problem (Deadlock)
1-2. Monitor
- Condition Variable
- wait(), signal()
2. 코딩 테스트 대비 알고리즘 학습
- Floyd-Warshall (LeetCode 743. Network Delay Time)
- Binary Search Tree, Reverse Inorder (RVL), Prefix Sum (LeetCode 1038. Binary Search Tree to Greater Sum Tree)
```

### Bounded-Buffer Problem (Producer-Consumer Problem)

![image.png](24_12_03_daily_certification%20151154b2a3b880e0aa96fdaefb31b237/image.png)

![image.png](24_12_03_daily_certification%20151154b2a3b880e0aa96fdaefb31b237/image%201.png)

### Readers-Writers Problem

![image.png](24_12_03_daily_certification%20151154b2a3b880e0aa96fdaefb31b237/image%202.png)

![image.png](24_12_03_daily_certification%20151154b2a3b880e0aa96fdaefb31b237/image%203.png)

### Dining Philosophers Problem

![image.png](24_12_03_daily_certification%20151154b2a3b880e0aa96fdaefb31b237/image%204.png)

![image.png](24_12_03_daily_certification%20151154b2a3b880e0aa96fdaefb31b237/image%205.png)

두번째 해결 방법

![image.png](24_12_03_daily_certification%20151154b2a3b880e0aa96fdaefb31b237/image%206.png)

### Monitor

![image.png](24_12_03_daily_certification%20151154b2a3b880e0aa96fdaefb31b237/image%207.png)

![image.png](24_12_03_daily_certification%20151154b2a3b880e0aa96fdaefb31b237/image%208.png)

### Bounded-Buffer Problem (Producer-Consumer Problem)

![image.png](24_12_03_daily_certification%20151154b2a3b880e0aa96fdaefb31b237/image%209.png)

### Readers-Writers Problem

### Dining Philosophers Problem

# Problem Solving (Algorithm & SQL)

### LeetCode **Network Delay Time**

[Network Delay Time - LeetCode](https://leetcode.com/problems/network-delay-time/)

```java
class Solution {
    private final int INF = 6_000 * 100 * 100 + 1;

    public int networkDelayTime(int[][] times, int n, int k) {
        int[][] dist = new int[n + 1][n + 1];

        for(int v = 1; v <= n; v++) {
            Arrays.fill(dist[v], INF);
            dist[v][v] = 0;
        }

        for(int i = 0; i < times.length; i++) {
            int u = times[i][0];
            int v = times[i][1];
            int w = times[i][2];

            dist[u][v] = w;
        }

        floydWarshall(n, dist);

        int result = 0;

        for(int v = 1; v <= n; v++) {
            if(dist[k][v] > result)
                result = dist[k][v];
        }

        return result == INF ? -1 : result;
    }

    private void floydWarshall(int n, int[][] dist) {
        for(int k = 1; k <= n; k++) {
            for(int i = 1; i <= n; i++) {
                for(int j = 1; j <= n; j++)
                    dist[i][j] = Math.min(dist[i][j], dist[i][k] + dist[k][j]);
            }
        }
    }
}
```

### LeetCode **Binary Search Tree to Greater Sum Tree**

[Binary Search Tree to Greater Sum Tree - LeetCode](https://leetcode.com/problems/binary-search-tree-to-greater-sum-tree/)

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
    private int prefixSum = 0;

    public TreeNode bstToGst(TreeNode root) {
        rvlInorder(root);
        return root;
    }

    private void rvlInorder(TreeNode root) {
        if(root == null)
            return;

        rvlInorder(root.right);
        root.val += prefixSum;
        prefixSum = root.val;
        rvlInorder(root.left);
    }
}
```