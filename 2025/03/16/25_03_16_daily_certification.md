# 25_03_16_daily_certification

```
[#075 koreii] 데일리인증 20250316
0. 자기소개서 마무리
1. 코딩 테스트 대비 알고리즘 학습
- Prefix Sum, Two-Pointer (BOJ 20442 ㅋㅋ루ㅋㅋ)
2. 코딩 테스트 대비 SQL 학습
- Session Variable, JOIN, GROUP BY (프로그래머스 상품을 구매한 회원 비율 구하기)
```

# Problem Solving (Algorithm & SQL)

### BOJ 20442 ㅋㅋ루ㅋㅋ

[](https://www.acmicpc.net/problem/20442)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static int N;
    
    private static String input;
    private static char[] kkrkk;
    
    private static int[] prefixSumK;    //    prefixSumK[i] : [1:i]까지 K의 개수
    private static int[] suffixSumK;    //    suffixSumK[i] : [i:N]의 K 개수
    private static int[] prefixSumR;	//	  prefixSumR[i] : [1:i]까지 R의 개수
    
    private static int start = 0;
    private static int end = 0;
    
    private static int answer = 0;
    
    public static void main(String[] args) throws IOException {
        input = br.readLine();
        N = input.length();
        
        kkrkk = new char[N + 2];
        prefixSumK = new int[N + 2];
        suffixSumK = new int[N + 2];
        prefixSumR = new int[N + 2];
        for(int i = 1; i <= N; i++)
            kkrkk[i] = input.charAt(i - 1);
        
        for(int i = 1; i <= N; i++) {
            prefixSumK[i] = prefixSumK[i - 1] + ((kkrkk[i] == 'K') ? 1 : 0);
            suffixSumK[N + 1 - i] = suffixSumK[N + 2 - i] + ((kkrkk[N + 1 - i] == 'K') ? 1 : 0);
            prefixSumR[i] = prefixSumR[i - 1] + ((kkrkk[i] == 'R') ? 1 : 0);
        }
        
        start = 1;
        end = N;
        
        while(start <= end) {
        	if(kkrkk[start] == 'R' && kkrkk[end] == 'R') {
        		int rCnt = prefixSumR[end] - prefixSumR[start - 1];	//	[start, end]에 있는 R의 개수
        		int left = prefixSumK[start];	//	start의 왼쪽에 있는 K 개수
        		int right = suffixSumK[end];	//	end의 오른쪽에 있는 K 개수
        		
        		answer = Math.max(answer, rCnt + 2 * Math.min(left, right));
        		
        		if(left < right)
        			start++;
        		else if(left > right)
        			end--;
        		else {
        			start++;
        			end--;
        		}
        	}
        	else {
        		if(kkrkk[start] == 'K')
        			start++;
        		if(kkrkk[end] == 'K')
        			end--;
        	}
        }
        
        System.out.println(answer);
    }    //    main-end
}    //    Main-class-end
```

### 프로그래머스 상품을 구매한 회원 비율 구하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/131534)

```sql
-- 코드를 입력하세요
select count(*) into @v1
from user_info
where year(joined) = 2021;

select year(t2.sales_date) year, month(t2.sales_date) month, count(distinct t1.user_id) purchased_users, round(count(distinct t1.user_id) / @v1, 1) purchased_ratio
from user_info t1 inner join online_sale t2
on t1.user_id = t2.user_id
where year(t1.joined) = 2021
group by year(t2.sales_date), month(t2.sales_date)
order by year, month;

```