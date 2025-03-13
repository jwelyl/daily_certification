# 25_03_13_daily_certification

# Problem Solving (Algorithm & SQL)

### 프로그래머스 단속카메라

[](https://school.programmers.co.kr/learn/courses/30/lessons/42884)

```java
import java.util.Arrays;

class Solution {
    public int solution(int[][] routes) {
        int answer = 0;
        int lastEndTime = Integer.MIN_VALUE;
        
        Arrays.sort(routes, (r1, r2) -> Integer.compare(r1[1], r2[1]) == 0 ? Integer.compare(r1[0], r2[0]) : Integer.compare(r1[1], r2[1]));
        
        for(int[] r : routes) {
            int sTime = r[0];
            int eTime = r[1];
            
            if(!(sTime <= lastEndTime && lastEndTime <= eTime)) {
                answer++;
                lastEndTime = eTime;
            }
        }
        
        return answer;
    }
}
```

### 프로그래머스 보호소에서 중성화한 동물

[](https://school.programmers.co.kr/learn/courses/30/lessons/59045)

```sql
-- 코드를 입력하세요
select t1.animal_id, t1.animal_type, t1.name
from animal_ins t1 inner join animal_outs t2
on t1.animal_id = t2.animal_id
where t1.sex_upon_intake like 'Intact%' and (t2.sex_upon_outcome like 'Spayed%' or t2.sex_upon_outcome like 'Neutered%')
order by t1.animal_id;
```

### 프로그래머스 년, 월, 성별 별 상품 구매 회원 수 구하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/131532)

```sql
-- 코드를 입력하세요
select year(t2.sales_date) year, month(t2.sales_date) month, t1.gender, count(distinct t1.user_id) users
from user_info t1 inner join online_sale t2
on t1.user_id = t2.user_id
group by year, month, gender
having gender is not null
order by year, month, gender;
```