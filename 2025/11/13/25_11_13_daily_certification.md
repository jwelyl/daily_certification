# 25_11_13_daily_certification

# To Do List

```
[#317 koreii] 데일리인증 20251113
0. 8시간 채우기 휴식 (0/8)
1. 취업 준비
- 일정 정리
2. 면접 + 면접 준비
- 화상 면접 (라이브 코딩 + CS)
- 화상 면접 (Java + 코드 리뷰)
- 면접 복기
- PT 면접 연습
3. 코딩 테스트
4. 알고리즘 문제 풀이
- LeetCode 995. Minimum Number of K Consecutive Bit Flips (Prefix Sum, Sliding Window)
```

```
[#136] 데일리인증 20251113 (317 / 365)
0. 8시간 채우기 휴식 (0/8)
1. 취업 준비
- 일정 정리
2. 면접 + 면접 준비
- 화상 면접 (라이브 코딩 + CS)
- 화상 면접 (Java + 코드 리뷰)
- 면접 복기
- PT 면접 연습
3. 코딩 테스트
4. 알고리즘 문제 풀이
- LeetCode 995. Minimum Number of K Consecutive Bit Flips (Prefix Sum, Sliding Window)
```

# Problem Solving (Algorithm & SQL)

### LeetCode  **995. Minimum Number of K Consecutive Bit Flips**

[Minimum Number of K Consecutive Bit Flips - LeetCode](https://leetcode.com/problems/minimum-number-of-k-consecutive-bit-flips/description/)

```java
class Solution {
    public int minKBitFlips(int[] nums, int k) {
        int answer = 0;
        int len = nums.length;
        int[] diff = new int[len + 1];
        int accSum = 0;

        for(int i = 0; i < len; i++) {
            accSum = (accSum + diff[i]) % 2;

            int bit = (nums[i] + accSum) % 2;

            if(bit != 1) {
                if(i > len - k) {
                    answer = -1;
                    break;
                }

                answer += 1;
                accSum = (accSum + 1) % 2;

                if(i + k < len)
                    diff[i + k] = (diff[i + k] + 1) % 2;
            }
        }
        
        return answer;
    }
}
```