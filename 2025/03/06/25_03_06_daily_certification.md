# 25_03_06_daily_certification

# Problem Solving (Algorithm & SQL)

### 프로그래머스 미로 탈출 명령어

[](https://school.programmers.co.kr/learn/courses/30/lessons/150365?language=java)

```java
import java.util.Stack;
import java.util.List;
import java.util.ArrayList;

class Solution {
    private final StringBuilder sb = new StringBuilder();
    
    private int N;
    private int M;
    private int R;
    private int C;
    private int K;
    
    private boolean[][][] visited;  //  visited[x][y][r] : (x, y)에 잔여 이동 횟수가 r번인 상태로 도착한 적이 있으면 true
    
    public String solution(int n, int m, int x, int y, int r, int c, int k) {
        N = n;
        M = m;
        R = r;
        C = c;
        K = k;
        
        int md = Math.abs(y - c) + Math.abs(x - r); //  두 지점 사이 최단거리
        
        visited = new boolean[N + 1][M + 1][K + 1];
        
        if(k < md || (k - md) % 2 == 1)
            return "impossible";
        
        visited[x][y][0] = true;
        dfs(0, x, y, new Stack<>());
        
        return sb.toString();
    }
    
    private boolean find = false;
    
    private void dfs(int nth, int cx, int cy, Stack<Character> stack) {
        if(find)
            return;
        
        if(nth == K) {
            if(cx == R && cy == C) {    //  k번만에 정확히 (r, c)에 도착한 경우
                find = true;
                
                List<Character> list = new ArrayList<>(stack);
                for(char dir : list)
                    sb.append(dir);
            }
            
            return;
        }
        
        for(int d = 0; d < 4; d++) {
            int nx = cx + dx[d];
            int ny = cy + dy[d];
            
            if(isIn(nx, ny) && !visited[nx][ny][nth + 1]) {
                visited[nx][ny][nth + 1] = true;
                stack.push(dirs[d]);
                dfs(nth + 1, nx, ny, stack);
                
                stack.pop();
            }
        }
        
        return;
    }
    
    private final char[] dirs = {'d', 'l', 'r', 'u'};
    
    private final int[] dx = {1, 0, 0, -1};
    private final int[] dy = {0, -1, 1, 0};
    
    private boolean isIn(int x, int y) {
        return (1 <= x && x <= N) && (1 <= y && y <= M);
    }
}
```

### 프로그래머스 특정 기간동안 대여 가능한 자동차들의 대여비용 구하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/157339)

```sql
-- 코드를 입력하세요
select crcc.car_id, crcc.car_type, round(crcc.daily_fee * 30 * (100 - crcdp.discount_rate) / 100) fee
from 
    car_rental_company_car crcc 
join
    car_rental_company_rental_history crcrh
on crcc.car_id = crcrh.car_id
inner join car_rental_company_discount_plan crcdp
on crcc.car_type = crcdp.car_type
where crcc.car_id not in 
(
    select car_id
    from car_rental_company_rental_history
    where not ('2022-11-30' < start_date || end_date < '2022-11-01')
) and crcc.car_type in ('SUV', '세단') and crcdp.duration_type = '30일 이상'
group by crcc.car_id
having 500000 <= fee and fee <= 2000000
order by fee desc, car_type, car_id desc;

```