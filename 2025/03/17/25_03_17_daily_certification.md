# 25_03_17_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 24525 SKK 문자열

[24525번: SKK 문자열](http://boj.ma/24525/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;

public class Main {
	private static final int ADD = 100_000;
	private static final int RANGE = 300_000;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    //	누적합에서 MIN_INDEX[i] : i + 100_000이 가장 먼저 나타나는 index
    private static final int[] MIN_INDEX = new int[RANGE + 1];
    private static String input;
    
    //	prefixS[i] : [1:i]의 S 개수
    private static int[] prefixS;
    //	prefixSum[i] : [1:i]의 합
    private static int[] prefixSum;
    
    private static int answer = -1;
    
    public static void main(String[] args) throws IOException {
        Arrays.fill(MIN_INDEX, Integer.MAX_VALUE);
    	input = br.readLine();
        
        prefixS = new int[input.length() + 1];
        prefixSum = new int[input.length() + 1];
        
        MIN_INDEX[ADD] = 0;
        
        for(int i = 1; i <= input.length(); i++) {
        	char ch = input.charAt(i - 1);
        	
        	prefixS[i] = prefixS[i - 1] + (ch == 'S' ? 1 : 0);
        	prefixSum[i] = prefixSum[i - 1] + (ch == 'S' ? 2 : (ch == 'K' ? -1 : 0));
        	
        	if(MIN_INDEX[prefixSum[i] + ADD] > i)
        		MIN_INDEX[prefixSum[i] + ADD] = i;
        }
        
        for(int i = 1; i <= input.length(); i++) {
        	int ps = prefixSum[i];
        	int idx = MIN_INDEX[ps + ADD];
        	
        	if(idx < i) {	//	[idx + 1:i]의 부분합이 0일 경우
        		int sCnt = prefixS[i] - prefixS[idx];
        		
        		if(sCnt >= 1)
        			answer = Math.max(answer, i - idx);
        	}
        }
        
        System.out.println(answer);
    }    //    main-end
}    //    Main-class-end
```

### 프로그래머스 조건별로 분류하여 주문상태 출력하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/131113)

```sql
-- 코드를 입력하세요
select order_id, product_id, date_format(out_date, '%Y-%m-%d'),
    (
        case
            when out_date <= '2022-05-01'
            then '출고완료'
            when out_date is null
            then '출고미정'
            else
                '출고대기'
        end
    ) '출고여부'
from food_order
order by order_id;
```

### 프로그래머스 대여 기록이 존재하는 자동차 리스트 구하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/157341)

```sql
-- 코드를 입력하세요
select t1.car_id
from car_rental_company_car t1
where t1.car_id in (
    select distinct t2.car_id 
    from car_rental_company_rental_history t2
    where month(start_date) = 10
) and t1.car_type = '세단'
order by t1.car_id desc;
```