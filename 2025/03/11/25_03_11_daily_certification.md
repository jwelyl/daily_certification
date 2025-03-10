# 25_03_11_daily_certification

# Problem Solving (Algorithm & SQL)

### 프로그래머스 선입 선출 스케줄링

[](https://school.programmers.co.kr/learn/courses/30/lessons/12920#)

```java
class Solution {
    private int maxTime = 0;    //  가장 오래 걸리는 작업 시간
    
    private int minTime = 0;    //  n개 이상 작업을 할 수 있는 최소 시간
    private int works = 0;      //  그 시간 동안 할 수 있는 작업의 수
    
    public int solution(int n, int[] cores) {
        int answer = 0;
        
        for(int time : cores)
            maxTime = Math.max(maxTime, time);
    
        parametricSearch(n, cores);
        
        works -= n;  //  mid 시간 동안 처리한 작업 수 중 초과한 작업 수
        
        for(int i = cores.length - 1; i >= 0; i--) {
            if(minTime % cores[i] == 0) {   //  i번째 코어가 minTime이 됐을때 작업을 딱 끝낼 수 있는 경우
                if(works == 0) {    //  초과 작업이 없었던 경우, i번째 코어가 끝낸 작업이 n번째 작업이었음
                    answer = i + 1;
                    break;
                }
                works--;    //  초과 작업 개수 1개 감소
            }
        }
        
        return answer;
    }
    
    private void parametricSearch(int n, int[] cores) {
        int start = 1;
        int end = maxTime * n;  //  작업 처리 속도가 가장 느린 코어가 모든 작업을 처리할때 걸리는 시간
        
        while(start <= end) {
            int mid = (start + end) / 2;    //  mid 시간 안에 코어들이 n개 작업들을 모두 처리할 수 있나 확인
            
            int cnt = cnt(n, cores, mid);   //  mid 시간 동안 처리할 수 있는 작업 개수
            
            if(cnt >= n) {      //  mid 시간 동안 n개 이상 처리 가능할 경우
                minTime = mid;  //  일단 저장
                end = mid - 1;  //  더 적은 시간에 가능한지 체크
                works = cnt;
            }
            else    //  mid 시간 동안 n개 이상 처리할 수 없는 경우
                start = mid + 1;    //  더 많은 시간에 n개 작업 끝낼 수 있는지 체크
        }
    }
    
    private int cnt(int n, int[] cores, int limit) {
        int cnt = cores.length;    //  limit 시간 동안 코어들이 처리할 수 있는 작업 수의 합 (최초에 코어 개수만큼 진입함)
        
        for(int time : cores)
            cnt += limit / time;
        
        return cnt;
    }
}
```

### 프로그래머스 주문량이 많은 아이스크림들 조회하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/133027)

```sql
-- 코드를 입력하세요
select t1.flavor
from first_half t1 inner join july t2
on t1.flavor = t2.flavor
group by t1.flavor
order by t1.total_order + sum(t2.total_order) desc
limit 3;
```