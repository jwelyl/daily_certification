# 24_12_02_daily_certification

```
[#337 koreii] 데일리인증 20241202
1. Spring AOP
- Logging, Retry AOP
2. Operating System
2-1. Process Synchronization
- Data Access
- Race Condition / Kernel Race Condition
- Critical Section
  - Mutual Exclusion
  - Progress
  - Bounded Waiting
- Peterson's Algorithm, Busy Waiting (=spin lock)
- Test_and_set
- Semaphore, Block & Wakeup (=sleep lock)
2-2. Deadlock
	-Starvation
3. 코딩 테스트 대비 알고리즘 학습
- Greedy, Sorting (LeetCode 455. Assign Cookies)
- Binary Search (LeetCode 704. Binary Search)
- Singly Linked List (LeetCode 206. Reverse Linked List)
- Two-Pointer (LeetCode 76. Minimum Window Substring)
```

Algorithm 1. Progress 위반

![image.png](24_12_02_daily_certification%2014f154b2a3b8803e84dbccc27efb271d/image.png)

Algorithm 2. Progress 위반

![스크린샷 2024-12-02 17.36.07.png](24_12_02_daily_certification%2014f154b2a3b8803e84dbccc27efb271d/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-12-02_17.36.07.png)

Algorithm 3 (Peterson’s Algorithm) → Mutex, Progress, Bounded Waiting 만족, Busy Waiting 문제

![image.png](24_12_02_daily_certification%2014f154b2a3b8803e84dbccc27efb271d/image%201.png)

Test_and_set → Mutex, Progress, Bounded Waiting 만족, Busy Waiting 문제S

![image.png](24_12_02_daily_certification%2014f154b2a3b8803e84dbccc27efb271d/image%202.png)

Semaphore 1 (Lock)

![image.png](24_12_02_daily_certification%2014f154b2a3b8803e84dbccc27efb271d/image%203.png)

Block & Wakeup

![image.png](24_12_02_daily_certification%2014f154b2a3b8803e84dbccc27efb271d/image%204.png)

![image.png](24_12_02_daily_certification%2014f154b2a3b8803e84dbccc27efb271d/image%205.png)

# Problem Solving (Algorithm & SQL)

### LeetCode **Assign Cookies**

[Assign Cookies - LeetCode](https://leetcode.com/problems/assign-cookies/description/)

```java
class Solution {
    public int findContentChildren(int[] g, int[] s) {
        Arrays.sort(g); //  욕심 적은 순으로 정렬
        Arrays.sort(s); //  쿠키 크기 순으로 오름차순 정렬

        int idx = 0;

        for(int i = 0; i < s.length && idx < g.length; i++) {
            if(s[i] >= g[idx])    //  i번째 쿠키에 idx번째 아이가 만족할 경우
                idx++;
        }

        return idx;
    }
}
```

### LeetCode Binary Search

[Binary Search - LeetCode](https://leetcode.com/problems/binary-search/description/)

```java
class Solution {
    public int search(int[] nums, int target) {
        int start = 0;
        int end = nums.length - 1;

        while(start <= end) {
            int mid = (start + end) / 2;
            if(target == nums[mid])
                return mid;

            if(target > nums[mid])
                start = mid + 1;
            else
                end = mid - 1;
        }

        return -1;
    }
}
```

### LeetCode **Reverse Linked List**

[Reverse Linked List - LeetCode](https://leetcode.com/problems/reverse-linked-list/description/)

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
    public ListNode reverseList(ListNode head) {
        ListNode ret = new ListNode(-1);    //  반환할 연결리스트의 더미노드, head는 ret.next임

        while(head != null) {
            ListNode retNext = ret.next;

            ListNode cur = head;
            ListNode next = cur.next;

            ret.next = cur;
            cur.next = retNext;

            head = next;    //  주어진 리스트의 다음 노드로 이동
        }

        return ret.next;
    }
}
```

### LeetCode **Minimum Window Substring**

[Minimum Window Substring - LeetCode](https://leetcode.com/problems/minimum-window-substring/description/)

```java
class Solution {
    private char[] sArr;
    private char[] tArr;

    private int m = 0;  //  문자열 s 길이
    private int n = 0;  //  문자열 t 길이

    private final int[] tCnts = new int[53];            //  문자열 t에 포함된 각 문자의 등장 횟수
    private final Set<Integer> tSet = new HashSet<>(); //  문자열 t에 등장하는 문자 종류 ('A' - 'Z' : 1 ~ 26, 'a' - 'z' : 27 ~ 52)  

    private int start = 0;
    private int end = 0;        //  two-pointers
    private final int[] sCnts = new int[53];    //  s의 [start, end]에 포함된 각 문자의 등장 횟수

    private int minLength;          //  t의 모든 문자(중복 포함)가 1번씩 포함되게 하는 s의 substring의 최소 길이
    private int minStart = -1;
    private int minEnd = -1;        //  그때의 s의 substring의 범위 [minStart, minEnd]

    public String minWindow(String s, String t) {
        sArr = s.toCharArray();
        tArr = t.toCharArray();

        m = s.length();
        n = t.length();
        minLength = m + 1;

        for(int i = 0; i < n; i++) {
            int idx = indexOfAlph(tArr[i]);
            tCnts[idx]++;
            tSet.add(idx);
        }

        sCnts[indexOfAlph(sArr[start])]++;  //  최초 [0, 0] 상태

        while(end < m) {
            if(ok()) {  //  이미 충분히 포함된 경우, 길이 줄여보기
                if(end - start + 1 < minLength) {   //  최소 길이 갱신했을 경우
                    minLength = end - start + 1;
                    minStart = start;
                    minEnd = end;
                }

                int idx = indexOfAlph(sArr[start]); //  사라질 문자
                sCnts[idx]--;
                start++;
            }
            else {  //  아직 부족할 경우
                if(end + 1 < m) { //  다음 문자가 존재할 경우
                    int idx = indexOfAlph(sArr[end + 1]); //  추가될 문자
                    sCnts[idx]++;
                }
                
                end++;
            }
        }

        return minLength == m + 1 ? "" : s.substring(minStart, minEnd + 1);
    }

    private boolean ok() {
        for(int idx : tSet) {
            if(tCnts[idx] > sCnts[idx]) //  s의 [start, end]에 포함된 idx 문자 개수가 t에 포함된 것보다 부족할 경우
                return false;
        }
        
        return true;
    }

    private int indexOfAlph(char ch) {
        if('A' <= ch && ch <= 'Z')
            return ch - 'A' + 1;
        else
            return ch - 'a' + 27;
    }
}
```