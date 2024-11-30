# 24_11_30_daily_certification

# Problem Solving (Algorithm & SQL)

### LeetCode Add Two Numbers

[Add Two Numbers - LeetCode](https://leetcode.com/problems/add-two-numbers/)

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
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode answer = new ListNode(0);
        ListNode cur = answer;

        while(l1 != null && l2 != null) {   //  l1, l2 둘 다 null이 아닐 동안
            cur.next = new ListNode((l1.val + l2.val) % 10);
            System.out.println("val = " + (l1.val + l2.val) % 10);

            if(l1.val + l2.val >= 10) {
                if(l1.next != null) //  l1의 다음 노드가 있을 경우, l1에게 1 주기
                    l1.next.val++;
                else if(l2.next != null)    //  l2의 다음 노드가 있을 경우, l2에게 1 주기
                    l2.next.val++;
                else    //  l1, l2 둘 다 다음 노드가 없을 경우, 1로 노드 만들어서 l1에 주기
                    l1.next = new ListNode(1);
            }

            l1 = l1.next;
            l2 = l2.next;
            cur = cur.next;
        }

        while(l1 != null) {
            if(l1.val >= 10) {
                cur.next = new ListNode(l1.val % 10);

                if(l1.next != null)
                    l1.next.val++;
                else
                    l1.next = new ListNode(1);
            }
            else
                cur.next = l1;

            l1 = l1.next;
            cur = cur.next;
        }
        while(l2 != null) {
            if(l2.val >= 10) {
                cur.next = new ListNode(l2.val % 10);

                if(l2.next != null)
                    l2.next.val++;
                else
                    l2.next = new ListNode(1);
            }
            else
                cur.next = l2;

            l2 = l2.next;
            cur = cur.next;
        }

        return answer.next;
    }
}
```

### 잡은 물고기 중 가장 큰 물고기의 길이 구하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/298515)

```sql
SELECT CONCAT(MAX(LENGTH), 'cm') AS MAX_LENGTH
FROM FISH_INFO;
```

### 자동차 대여 기록에서 장기/단기 대여 구분하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/151138)

```sql
SELECT HISTORY_ID, CAR_ID, DATE_FORMAT(START_DATE, '%Y-%m-%d') AS START_DATE, DATE_FORMAT(END_DATE, '%Y-%m-%d') AS END_DATE,
CASE 
    WHEN DATEDIFF(END_DATE, START_DATE) + 1 >= 30
    THEN "장기 대여"
    ELSE "단기 대여"
END AS RENT_TYPE
FROM CAR_RENTAL_COMPANY_RENTAL_HISTORY
WHERE YEAR(start_date) = 2022 AND MONTH(start_date) = 9
ORDER BY HISTORY_ID DESC;
```

### 자동차 평균 대여 기간 구하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/157342)

```sql
SELECT ch.CAR_ID, ROUND(AVG(DATEDIFF(ch.END_DATE, ch.START_DATE) + 1), 1) AS AVERAGE_DURATION
FROM CAR_RENTAL_COMPANY_RENTAL_HISTORY ch
GROUP BY ch.CAR_ID
HAVING AVERAGE_DURATION >= 7
ORDER BY AVERAGE_DURATION DESC, ch.CAR_ID DESC;
```

### 물고기 종류 별 대어 찾기

[](https://school.programmers.co.kr/learn/courses/30/lessons/293261)

```sql
SELECT ID, FISH_NAME, LENGTH
FROM FISH_INFO
NATURAL INNER JOIN
FISH_NAME_INFO
WHERE (FISH_TYPE, LENGTH) IN
(SELECT FISH_TYPE, MAX(LENGTH)
FROM FISH_INFO
GROUP BY FISH_TYPE)
ORDER BY ID;
```