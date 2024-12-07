# 24_12_06_daily_certification

![image.png](24_12_06_daily_certification%20153154b2a3b880b5b619d508411147c9/image.png)

![image.png](24_12_06_daily_certification%20153154b2a3b880b5b619d508411147c9/image%201.png)

**Resource Allocation Graph Algorithm (자원 당 인스턴스가 하나인 경우)**

![image.png](24_12_06_daily_certification%20153154b2a3b880b5b619d508411147c9/image%202.png)

~~마지막 그림의 경우에는 데드락 발생 가능성이 조금이라도 있으므로 자원을 할당하지 않는다.~~

P2가 R2를 요청하고 R2가 P2에 할당되면 Cycle이 발생한다.

**Banker’s Algorithm (자원 당 인스턴스가 두개 이상인 경우)**

![image.png](24_12_06_daily_certification%20153154b2a3b880b5b619d508411147c9/image%203.png)

![image.png](24_12_06_daily_certification%20153154b2a3b880b5b619d508411147c9/image%204.png)

프로세스가 평생 사용할 최대 자원을 Max로 미리 계산해둔 상태

Need : Process가 최대로 요청할 수 있는 자원량

P0가 A 1개를 요청해도 주지 않음. Available만 보면 충분히 줄 수 있지만 Need가 Available보다 크므로 주지 않음 1개만 요청해서 사용하고 반납할지 Need를 모두 만족할때까지 반납하지 않을지 모르니까.

촤악의 경우를 상정함 (요청 프로세스가 최대 요청을 할 것이라고 가정하고 최대 요청을 받아들일 수 있을 경우에만 해당 프로세스의 요청을 받아들임)

P1, P3, P4, P2, P0의 요청 순서는 가능함

받아들일 수 있는 요청도 안받아들임. 딱 봐도 졸라게 비효율적임.

![image.png](24_12_06_daily_certification%20153154b2a3b880b5b619d508411147c9/image%205.png)

P4의 요청이 Available보다 크므로 요청을 수락할 수 없다.

P0의 요청 (0, 2, 0) 자체는 수락 가능하지만 P0의 최대 요청 (7, 4, 3)을 수락할 수 없으므로 요청을 수락하지 않는다. 준다고 Deadlock이 되는것은 아니지만 주지 않는다.

# Problem Solving (Algorithm & SQL)

### LeetCode Design HashMap

[House Robber - LeetCode](https://leetcode.com/problems/house-robber/description/)

```java
class Solution {
    public int rob(int[] nums) {
        if(nums.length == 1)    //  털 집이 하나뿐일 경우
            return nums[0];
        else if(nums.length == 2)   //  털 집이 두 집 뿐일 경우, 둘 다 털 수는 없음
            return Math.max(nums[0], nums[1]);  //  둘 중 더 비싼 집 털기

        //  dp[k] : 0번쨰 집부터 ~ k번째 집까지 안걸리게 털었을때 최대 이익
        int[] dp = new int[nums.length];
        dp[0] = nums[0];
        dp[1] = Math.max(nums[0], nums[1]);

        for(int i = 2; i < nums.length; i++)
            dp[i] = Math.max(dp[i - 2] + nums[i], dp[i - 1]);

        return dp[nums.length - 1];
    }
}
```

### LeetCode **Serialize and Deserialize Binary Tree**

[Serialize and Deserialize Binary Tree - LeetCode](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/description/)

```java
import java.util.StringTokenizer;

/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Codec {
    private List<String> bfs(TreeNode root) {
        List<String> list = new ArrayList<>();

        if(root != null) {
            Queue<TreeNode> queue = new LinkedList<>();
            queue.offer(root);
            list.add(Integer.toString(root.val));

            while(!queue.isEmpty()) {
                TreeNode cur = queue.poll();

                if(cur.left == null)
                    list.add("null");
                else {
                    list.add(Integer.toString(cur.left.val));
                    queue.offer(cur.left);
                }

                if(cur.right == null)
                    list.add("null");
                else {
                    list.add(Integer.toString(cur.right.val));
                    queue.offer(cur.right);
                }
            }
        }

        return list;
    }

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        List<String> serialized = bfs(root);

        sb.append("[");

        int minus = 0;
        for(int i = serialized.size() - 1; i >= 0; i--) {
            String value = serialized.get(i);

            if(!value.equals("null"))
                break;

            minus++;
        }

        for(int i = 0; i < serialized.size() - minus; i++) {
            sb.append(serialized.get(i));
            if(i != serialized.size() - minus - 1)
                sb.append(",");
        }

        sb.append("]");

        return sb.toString();
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        data = data.substring(1, data.length() - 1);

        StringTokenizer st = new StringTokenizer(data, ",");
        int size = st.countTokens();

        if(size == 0)
            return null;

        String[] vals = new String[size + 1];
        TreeNode[] nodes = new TreeNode[size + 1];

        for(int i = 1; i <= size; i++) {
            vals[i] = st.nextToken();
            nodes[i] = vals[i].equals("null") ? null : new TreeNode(Integer.parseInt(vals[i]));
        }
        System.out.println();

        int pIdx = 1;
        int cIdx = 2;

        while(pIdx <= size || cIdx <= size) {
            TreeNode parent = nodes[pIdx];
            pIdx++;

            if(parent == null)
                continue;
            
            if(cIdx <= size) {
                TreeNode left = nodes[cIdx];
                parent.left = left;

                if(cIdx + 1 <= size) {
                    TreeNode right = nodes[cIdx + 1];
                    parent.right = right;
                }
            }

            cIdx += 2;
        }

        return nodes[1];
    }
}

// Your Codec object will be instantiated and called as such:
// Codec ser = new Codec();
// Codec deser = new Codec();
// TreeNode ans = deser.deserialize(ser.serialize(root));
```