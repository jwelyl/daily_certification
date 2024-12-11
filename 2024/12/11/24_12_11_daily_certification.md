# 24_12_11_daily_certification

![image.png](24_12_11_daily_certification%20158154b2a3b880ee981ef45b0c311558/image.png)

![스크린샷 2024-12-11 21.24.39.png](24_12_11_daily_certification%20158154b2a3b880ee981ef45b0c311558/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-12-11_21.24.39.png)

![image.png](24_12_11_daily_certification%20158154b2a3b880ee981ef45b0c311558/image%201.png)

![image.png](24_12_11_daily_certification%20158154b2a3b880ee981ef45b0c311558/image%202.png)

**Optimal Algorithm**

이론상 가장 성능이 좋은 알고리즘이다.

하지만 실제 시스템에서 미래를 알수는 없다. 미래에 어떤 페이지가 참조될 지 어찌 알겠는가?

Offline Optimal Algorithm (실제 시스템에서 Online으로 사용할 수는 없다.)

![image.png](24_12_11_daily_certification%20158154b2a3b880ee981ef45b0c311558/image%203.png)

Optimal Algorithm이므로 다른 어떤 알고리즘을 사용해도 6번보다 적게 Page Fault를 낼 수는 없다.

아무리 좋은 알고리즘을 만들어도 Page Fault를 6번 이상으로 내게 된다. (Upper Bound)

어떤 알고리즘을 개발했는데 Page Fault가 6번 난다면 더 개선시킬 여지가 없다.

**FIFO**

![스크린샷 2024-12-11 23.36.28.png](24_12_11_daily_certification%20158154b2a3b880ee981ef45b0c311558/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-12-11_23.36.28.png)

Frame이 많아졌는데 Page Fault가 오히려 더 많이 발생한다??? (FIFO Anomaly)

메모리를 업그레이드했는데 성능이 더 떨어짐

**LRU**

Online Algorithm으로 실제 시스템에 사용된다. 미래는 알 수 없다.

미래를 알수 없다면 과거를 보거라.

![image.png](24_12_11_daily_certification%20158154b2a3b880ee981ef45b0c311558/image%204.png)

**LFU**

![image.png](24_12_11_daily_certification%20158154b2a3b880ee981ef45b0c311558/image%205.png)

![image.png](24_12_11_daily_certification%20158154b2a3b880ee981ef45b0c311558/image%206.png)

LFU에 의해 4번 page를 쫓아냈는데 그 이후에 4번 page만 주구장창 참조되면??

**LRU, LFU 구현**

![image.png](24_12_11_daily_certification%20158154b2a3b880ee981ef45b0c311558/image%207.png)

LRU : Sorted Doubly Linked List, Time Complexity O(1)

- 참조된지 오래된 Page가 DLL의 head에, 최근 참조된 Page가 Tail에 있음
- 가장 최근에 참조되면 무조건 tail로 이동시키면 되기 때문에 줄세우기가 쉽다.

LFU 

- LRU와 같은 방법으로 구현할 경우, 한번 더 참조된다고 참조 횟수를 다른 Process와 비교할 수 없다. LRU는 한번 참조되면 바로 tail로 이동 가능한 것과 다르다. 비교를 통해 어디로 내려가야 할지 계속 비교해야 한다. Time Complexity O(N)

![image.png](24_12_11_daily_certification%20158154b2a3b880ee981ef45b0c311558/image%208.png)

- Heap을 이용해 참조 횟수가 가장 적은 Page 추출, Page 참조 시 위치 조정 등을 O(logN)에 할 수 있다.
- O(N)과 O(logN)은 하늘과 땅 차이이다. N = 10^6이면 10^6과 20 정도의 차이이다.

# Problem Solving (Algorithm & SQL)

### LeetCode ****Maximum Subarray

[Maximum Subarray - LeetCode](https://leetcode.com/problems/maximum-subarray/description/)

**Divide And Conquer (Time Complexity : O(NlogN))**

```java
class Solution {
    public int maxSubArray(int[] nums) {
        return dnc(nums, 0, nums.length - 1);
    }

    private int dnc(int[] nums, int start, int end) {
        if(start == end)
            return nums[start];

        int mid = (start + end) / 2;
        int leftMax = dnc(nums, start, mid);
        int rightMax = dnc(nums, mid + 1, end);

        int leftPartSum = nums[mid];
        int rightPartSum = nums[mid + 1];
        int maxLeftPartSum = leftPartSum;
        int maxRightPartSum = rightPartSum;

        for(int i = mid - 1; i >= start; i--) {
            leftPartSum += nums[i];
            if(leftPartSum > maxLeftPartSum)
                maxLeftPartSum = leftPartSum;
        }

        for(int i = mid + 2; i <= end; i++) {
            rightPartSum += nums[i];
            if(rightPartSum > maxRightPartSum)
                maxRightPartSum = rightPartSum;
        }

        return Math.max(Math.max(leftMax, rightMax), maxLeftPartSum + maxRightPartSum);
    }
}
```

**Dynamic Programming (Time Complexity : O(N), Space Complexity : O(N))**

```java
class Solution {
    public int maxSubArray(int[] nums) {
        return dnc(nums, 0, nums.length - 1);
    }

    private int dnc(int[] nums, int start, int end) {
        if(start == end)
            return nums[start];

        int mid = (start + end) / 2;
        int leftMax = dnc(nums, start, mid);
        int rightMax = dnc(nums, mid + 1, end);

        int leftPartSum = nums[mid];
        int rightPartSum = nums[mid + 1];
        int maxLeftPartSum = leftPartSum;
        int maxRightPartSum = rightPartSum;

        for(int i = mid - 1; i >= start; i--) {
            leftPartSum += nums[i];
            if(leftPartSum > maxLeftPartSum)
                maxLeftPartSum = leftPartSum;
        }

        for(int i = mid + 2; i <= end; i++) {
            rightPartSum += nums[i];
            if(rightPartSum > maxRightPartSum)
                maxRightPartSum = rightPartSum;
        }

        return Math.max(Math.max(leftMax, rightMax), maxLeftPartSum + maxRightPartSum);
    }
}
```

**Kadane’s Algorithm (Time Complexity : O(N), Space Complexity : O(1))**

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int answer = nums[0];
        int partialSum = nums[0];

        for(int i = 1; i < nums.length; i++) {
            partialSum = (partialSum + nums[i] < nums[i]) ? nums[i] : partialSum + nums[i];
            answer = Math.max(answer, partialSum);
        }

        return answer;
    }
}
```

### LeetCode **Two Sum II - Input Array Is Sorted**

[Two Sum II - Input Array Is Sorted - LeetCode](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/description/)

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int[] answer = new int[2];

        for(int start = 0; start < numbers.length - 1; start++) {
            int idx = binarySearch(numbers, start + 1, numbers.length - 1, target - numbers[start]);

            if(idx != -1) {
                answer[0] = start + 1;
                answer[1] = idx + 1;
                break;
            }    
        }

        return answer;
    }

    private int binarySearch(int[] numbers, int start, int end, int target) {
        while(start <= end) {
            int mid = (start + end) / 2;

            if(target == numbers[mid])
                return mid;

            if(target < numbers[mid])
                end = mid - 1;
            else
                start = mid + 1;
        }

        return -1;
    }
}
```

### LeetCode Invert Binary Tree

[Invert Binary Tree - LeetCode](https://leetcode.com/problems/invert-binary-tree/description/)

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
    public TreeNode invertTree(TreeNode root) {
        postorder(root);
        return root;
    }

    private void postorder(TreeNode root) {
        if(root == null)
            return;
    
        postorder(root.left);
        postorder(root.right);
        
        TreeNode tmp = root.left;
        root.left = root.right;
        root.right = tmp;
    }
}
```

### LeetCode **Convert Sorted Array to Binary Search Tree**

[Convert Sorted Array to Binary Search Tree - LeetCode](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)

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
    public TreeNode sortedArrayToBST(int[] nums) {
        return makeBST(0, nums.length - 1, nums);
    }

    private TreeNode makeBST(int start, int end, int[] nums) {
        if(start > end)
            return null;

        int mid = (start + end) / 2;
        TreeNode root = new TreeNode(nums[mid]);

        root.left = makeBST(start, mid - 1, nums);
        root.right = makeBST(mid + 1, end, nums);

        return root;
    }
}
```