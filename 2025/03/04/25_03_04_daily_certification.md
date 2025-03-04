# 25_03_04_daily_certification

# Problem Solving (Algorithm & SQL)

### 프로그래머스 최적의 행렬 곱셈

[](https://school.programmers.co.kr/learn/courses/30/lessons/12942)

```java
import java.util.Arrays;

class Solution {
    private int n;      //  곱하려는 행렬 개수
    private int[][] dp; //  dp[i][j] : i번쨰 행렬부터 j번째 행렬까지 곱할때 최소 곱연산 수 
    
    public int solution(int[][] m) {
        n = m.length;
        
        dp = new int[n][n];
        for(int y = 0; y < n; y++) {
            Arrays.fill(dp[y], Integer.MAX_VALUE);
            dp[y][y] = 0;
        }
        
        for(int k = 1; k < n; k++) {
            for(int y = 0; y + k < n; y++) {
                int x = y + k;
                
                for(int mid = y; mid < x; mid++)
                    dp[y][x] = Math.min(dp[y][x], dp[y][mid] + dp[mid + 1][x] + m[y][0] * m[mid][1] * m[x][1]);
            }
        }
        
        return dp[0][n - 1];
    }
}
```

### BOJ 11049 행렬 곱셈 순서

[11049번: 행렬 곱셈 순서](http://boj.ma/11049/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;      		//  곱하려는 행렬 개수
	private static int[][] m;		//	m[i] : i번째 행렬의 {행 개수, 열 개수}
    private static int[][] dp; 		//  dp[i][j] : i번쨰 행렬부터 j번째 행렬까지 곱할때 최소 곱연산 수 
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		m = new int[N][2];
		dp = new int[N][N];
		for(int y = 0; y < N; y++) {
            Arrays.fill(dp[y], Integer.MAX_VALUE);
            dp[y][y] = 0;
        }
		
		for(int i = 0; i < N; i++) {
			st = new StringTokenizer(br.readLine());
			m[i][0] = Integer.parseInt(st.nextToken());
			m[i][1] = Integer.parseInt(st.nextToken());
		}
		
		for(int k = 1; k < N; k++) {
            for(int y = 0; y + k < N; y++) {
                int x = y + k;
                
                for(int mid = y; mid < x; mid++)
                    dp[y][x] = Math.min(dp[y][x], dp[y][mid] + dp[mid + 1][x] + m[y][0] * m[mid][1] * m[x][1]);
            }
        }
        
        System.out.println(dp[0][N - 1]);
	}	//	main-end
}	//	Main-class-end
```

### 프로그래머스 자동차 대여 기록에서 대여중 / 대여 가능 여부 구분하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/157340)

```sql
-- 코드를 입력하세요
select distinct
    car_id, 
    (
        case
            when car_id in (
                select car_id
                from car_rental_company_rental_history
                where '2022-10-16' between start_date and end_date
            ) then '대여중'
            else '대여 가능'
        end
    ) as availability
from car_rental_company_rental_history
order by car_id desc;
```