# 24_12_14_daily_certification

```
[#348 koreii] 데일리인증 20241213
1. 코딩 테스트 대비 알고리즘 학습
- DP (LeetCode 70. Climbing Stairs)
- Monotonic Stack (LeetCode 739. Daily Temperatures)
- String, Counting (LeetCode 242. Valid Anagram)
- Counting Sort, In-place sort (LeetCode 75. Sort Colors)
```

# Problem Solving (Algorithm & SQL)

### LeetCode **Climbing Stairs**

[Climbing Stairs - LeetCode](https://leetcode.com/problems/climbing-stairs/description/)

```java
class Solution {
    public int climbStairs(int n) {
        int answer = 0;

        int f0 = 1;
        int f1 = 1;
        int f = 1;

        for(int i = 2; i <= n; i++) {
            f = f0 + f1;
            f0 = f1;
            f1 = f;
        }

        return f;
    }
}
```

### LeetCode **Daily Temperatures**

[Daily Temperatures - LeetCode](https://leetcode.com/problems/daily-temperatures/description/)

```java
class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        int[] answer = new int[temperatures.length];
        Stack<int[]> stack = new Stack<>();

        for(int i = temperatures.length - 1; i >= 0; i--) {
            while(!stack.isEmpty() && stack.peek()[0] <= temperatures[i])
                stack.pop();

            if(!stack.isEmpty())
                answer[i] = stack.peek()[1] - i;

            stack.push(new int[] {temperatures[i], i});
        }

        return answer;
    }
}
```

### LeetCode Valid Anagram

[Valid Anagram - LeetCode](https://leetcode.com/problems/valid-anagram/description/)

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        char[] sArr = s.toCharArray();
        char[] tArr = t.toCharArray();

        Arrays.sort(sArr);
        Arrays.sort(tArr);

        return new String(sArr).equals(new String(tArr));
    }
}
```

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        final int[] cnts = new int[26];
        
        for(int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            cnts[ch - 'a']++;
        }

        for(int i = 0; i < t.length(); i++) {
            char ch = t.charAt(i);
            cnts[ch - 'a']--;
        }

        for(int i = 0; i < 26; i++) {
            if(cnts[i] != 0)
                return false;
        }

        return true;
    }
}
```

### LeetCode **Sort Colors**

[Sort Colors - LeetCode](https://leetcode.com/problems/sort-colors/description/)

```java
class Solution {
    public void sortColors(int[] nums) {
        int[] cnts = new int[3];

        for(int num : nums)
            cnts[num]++;

        int idx = 0;
        for(int num = 0; num < 3; num++) {
            for(int i = 0; i < cnts[num]; i++) {
                nums[idx] = num;
                idx++;
            }
        }
    }
}
```