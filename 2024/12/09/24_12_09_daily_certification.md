# 24_12_09_daily_certification

```
[#344 koreii] 데일리인증 20241209
1. 코딩 테스트 대비 알고리즘 학습
- String, Backtracking (LeetCode 17. Letter Combinations of a Phone Number)
- Priority Queue, HashSet (LeetCode 347. Top K Frequent Elements)
```

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