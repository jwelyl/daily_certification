# 24_12_09_daily_certification

![image.png](24_12_09_daily_certification%20157154b2a3b880bcb5cbedd335f4ecb3/image.png)

![image.png](24_12_09_daily_certification%20157154b2a3b880bcb5cbedd335f4ecb3/image%201.png)

![image.png](24_12_09_daily_certification%20157154b2a3b880bcb5cbedd335f4ecb3/image%202.png)

![image.png](24_12_09_daily_certification%20157154b2a3b880bcb5cbedd335f4ecb3/image%203.png)

![image.png](24_12_09_daily_certification%20157154b2a3b880bcb5cbedd335f4ecb3/image%204.png)

![image.png](24_12_09_daily_certification%20157154b2a3b880bcb5cbedd335f4ecb3/image%205.png)

# Problem Solving (Algorithm & SQL)

### LeetCode Letter Combinations of a Phone Number

[Letter Combinations of a Phone Number - LeetCode](https://leetcode.com/problems/letter-combinations-of-a-phone-number/)

```java
class Solution {
    private final char[][] arr = {
        {},
        {},
        {'a', 'b', 'c'},
        {'d', 'e', 'f'},
        {'g', 'h', 'i'},
        {'j', 'k', 'l'},
        {'m', 'n', 'o'},
        {'p', 'q', 'r', 's'},
        {'t', 'u', 'v'},
        {'w', 'x', 'y', 'z'},
    };

    private final List<String> answer = new ArrayList<>();

    public List<String> letterCombinations(String digits) {
        if(digits.equals(""))
            return answer;

        dfs(0, new char[digits.length()], digits);
        return answer;
    }

    private void dfs(int nth, char[] selected, String digits) {
        if(nth == digits.length()) {
            answer.add(new String(selected));
            return;
        }

        int idx = digits.charAt(nth) - '0';

        for(int i = 0; i < arr[idx].length; i++) {
            selected[nth] = arr[idx][i];
            dfs(nth + 1, selected, digits);
        }
    }
}
```

### LeetCode Top K Frequent Elements

[Top K Frequent Elements - LeetCode](https://leetcode.com/problems/top-k-frequent-elements/description/)

```java
class Solution {
    private final int[] cnts = new int[20_001];
    private final PriorityQueue<int[]> pq = new PriorityQueue<>((elm1, elm2) -> Integer.compare(elm2[1], elm1[1]));
    private final Set<Integer> set = new HashSet<>();

    public int[] topKFrequent(int[] nums, int k) {
        for(int num : nums) {
            cnts[num + 10_000]++;
            set.add(num);
        }
        
        for(int num : set)
            pq.offer(new int[] {num, cnts[num + 10_000]});

        int[] answer = new int[Math.min(k, set.size())];

        for(int i = 0; i < answer.length; i++) {
            int[] out = pq.poll();
            answer[i] = out[0];
        }

        return answer;
    }
}
```

### LeetCode **Merge Two Sorted Lists**

[Merge Two Sorted Lists - LeetCode](https://leetcode.com/problems/merge-two-sorted-lists/)

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
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        ListNode list = new ListNode(Integer.MIN_VALUE);
        ListNode prev = list;

        while(list1 != null && list2 != null) {
            if(list1.val < list2.val) {
                prev.next = list1;
                prev = list1;
                list1 = list1.next;
            }
            else {
                prev.next = list2;
                prev = list2;
                list2 = list2.next;
            }
            prev.next = null;
        }

        while(list1 != null) {
            prev.next = list1;
            prev = list1;
            list1 = list1.next;
            prev.next = null;
        }

        while(list2 != null) {
            prev.next = list2;
            prev = list2;
            list2 = list2.next;
            prev.next = null;
        }

        return list.next;
    }
}
```

### LeetCode **Sliding Window Maximum**

[Sliding Window Maximum - LeetCode](https://leetcode.com/problems/sliding-window-maximum/description/)

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int[] answer = new int[nums.length - k + 1];
        TreeMap<Integer, Integer> map = new TreeMap<>();

        //  최초 k개 [0, k]에 포함된 수들을 treeMap에 넣기
        for(int i = 0; i < k; i++)
            map.put(nums[i], map.getOrDefault(nums[i], 0) + 1);    //  i번째 수 개수 1 증가

        answer[0] = map.lastKey();  //  treeMap에 존재하는 수 중 가장 큰 수

        for(int i = 1; i <= nums.length - k; i++) {
            int outNum = nums[i - 1];       //  슬라이딩 윈도우가 움직이면서 빠질 수
            int inNum = nums[i + k - 1];    //  슬라이딩 윈도우가 움직이면서 추가될 수

            int outNumCnt = map.get(outNum);
            if(outNumCnt == 1)  //  마지막 1개 남았을 경우
                map.remove(outNum); // treeMap에서 제거
            else    //  2개 이상일 경우
                map.put(outNum, outNumCnt - 1); //  개수 1개 감소
            map.put(inNum, map.getOrDefault(inNum, 0) + 1);    //  inNum 개수 1 증가

            answer[i] = map.lastKey();
        }

        return answer;
    }
}
```