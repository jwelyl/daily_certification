# 25_03_10_daily_certification

# Problem Solving (Algorithm & SQL)

### 프로그래머스 다단계 칫솔 판매

[](https://school.programmers.co.kr/learn/courses/30/lessons/77486)

```java
import java.util.Map;
import java.util.HashMap;
import java.util.Arrays;

class Solution {
    private final int NONE = -1;
    private final int CENTER = 0;
    
    private int num = 1;
    
    private String[] numToName;
    private final Map<String, Integer> nameToNum = new HashMap<>();
    
    private int N;
    private int[] parents;
    
    private int[] answer;
    
    public int[] solution(String[] enroll, String[] referral, String[] seller, int[] amount) {
        N = enroll.length;
        
        parents = new int[N + 1];
        numToName = new String[N + 1];
        Arrays.fill(parents, NONE);
        
        numToName[CENTER] = "-";
        nameToNum.put("-", CENTER);
        
        answer = new int[N];
        
        for(int i = 1; i <= N; i++) {
            String name = enroll[i - 1];
            numToName[i] = name;
            nameToNum.put(name, i);
        }
        
        for(int i = 1; i <= N; i++) {
            int referrer = nameToNum.get(referral[i - 1]);
            
            parents[i] = referrer;
        }
        
        for(int i = 0; i < seller.length; i++) {
            int cur = nameToNum.get(seller[i]);
            int money = amount[i] * 100;

            while(true) {
                if(cur == CENTER)
                    break;
                
                if(money < 10) {
                    answer[cur - 1] += money;
                    break;
                }
                
                int p = money / 10;
                answer[cur - 1] += (money - p);
                
                cur = parents[cur];
                money = p;
            }
        }
        
        return answer;
    }
}
```

### 프로그래머스 식품분류별 가장 비싼 식품의 정보 조회하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/131116)

```sql
-- 코드를 입력하세요
select t1.category, t1.price as max_price, t1.product_name
from food_product t1 
inner join
(
    select category, max(price) max_price
    from food_product
    group by category
    having category in ('과자', '국', '김치', '식용유')
) t2
on 
    t1.category = t2.category and t1.price = t2.max_price
order by max_price desc;
```