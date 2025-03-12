# 25_03_12_daily_certification

# Problem Solving (Algorithm & SQL)

### 프로그래머스 블록 이동하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/60063)

```java
import java.util.Queue;
import java.util.LinkedList;

class Solution {
    private final int HORI = 0;     //  수평
    private final int VERT = 1;     //  수직
    
    private int N;
    private int[][] map;
    
    //  visited[y][x][HORI] : (y, x)를 왼쪽 좌표로 방문했으면 true
    //  visited[y][x][VERT] : (y, x)를 위쪽 좌표로 방문했으면 true
    private boolean[][][] visited;
    
    public int solution(int[][] board) {
        map = board;
        N = map.length;
        
        visited = new boolean[N][N][2];
        
        return bfs();
    }
    
    private int bfs() {
        Queue<int[]> queue = new LinkedList<>();
        visited[0][0][HORI] = true;
        queue.offer(new int[] {0, 0, HORI, 0});
        
        while(!queue.isEmpty()) {
            int[] cur = queue.poll();
            int cy1 = cur[0];
            int cx1 = cur[1];
            int cdir = cur[2];
            int ctime = cur[3];
            
            int cy2 = cy1 + dy[cdir];
            int cx2 = cx1 + dx[cdir];
            
            //  1. 회전 없이 그냥 이동하는 경우
            for(int d = 0; d < 4; d++) {
                int ny1 = cy1 + dy[d];
                int nx1 = cx1 + dx[d];  //  (cy1, cx1)이 이동할 좌표
                int ny2 = cy2 + dy[d];
                int nx2 = cx2 + dx[d];  //  (cy2, cx2)가 이동할 좌표
                
                if(canMove(ny1, nx1) && canMove(ny2, nx2) && !visited[ny1][nx1][cdir]) {
                    if(isEnd(ny1, nx1) || isEnd(ny2, nx2))
                        return ctime + 1;
                
                    visited[ny1][nx1][cdir] = true;
                    queue.offer(new int[] {ny1, nx1, cdir, ctime + 1});
                }
            }
            
            //  2. 회전하는 경우
            int ndir = cdir == HORI ? VERT : HORI;
            int ny1 = -1;
            int nx1 = -1;
            int ny2 = -1;
            int nx2 = -1;
            
            //  2-1. 현재 상태가 수평한 상태인 경우
            if(cdir == HORI) {
                //  2-1-1. 위로 회전할 경우
                if(canMove(cy1 - 1, cx1) && canMove(cy1 - 1, cx1 + 1)) {    //  회전이 가능할 경우 (공간 확보됨)
                    //  2-1-1-1. (cy1, cx1)을 중심으로 회전할 경우
                    ny1 = cy1 - 1;
                    nx1 = cx1;
                    ny2 = cy1;
                    nx2 = cx1;
                    
                    if(!visited[ny1][nx1][ndir]) {  //  아직 방문하지 않은 경우
                        if(isEnd(ny1, nx1) || isEnd(ny2, nx2))
                            return ctime + 1;
                        
                        visited[ny1][nx1][ndir] = true;
                        queue.offer(new int[] {ny1, nx1, ndir, ctime + 1});
                    }
                    
                    //  2-1-1-2. (cy1, cx1)을 중심으로 회전하지 않을 경우
                    ny1 = cy1 - 1;
                    nx1 = cx1 + 1;
                    ny2 = cy1;
                    nx2 = cx1 + 1;
                    
                    if(!visited[ny1][nx1][ndir]) {  //  아직 방문하지 않은 경우
                        if(isEnd(ny1, nx1) || isEnd(ny2, nx2))
                            return ctime + 1;
                        
                        visited[ny1][nx1][ndir] = true;
                        queue.offer(new int[] {ny1, nx1, ndir, ctime + 1});
                    }
                }
                //  2-1-2. 아래로 회전할 경우
                if(canMove(cy1 + 1, cx1) && canMove(cy1 + 1, cx1 + 1)) {    //  회전이 가능할 경우 (공간 확보됨)
                    //  2-1-2-1. (cy1, cx1)을 중심으로 회전할 경우
                    ny1 = cy1;
                    nx1 = cx1;
                    ny2 = cy1 + 1;
                    nx2 = cx1;
                    
                    if(!visited[ny1][nx1][ndir]) {  //  아직 방문하지 않은 경우
                        if(isEnd(ny1, nx1) || isEnd(ny2, nx2))
                            return ctime + 1;
                        
                        visited[ny1][nx1][ndir] = true;
                        queue.offer(new int[] {ny1, nx1, ndir, ctime + 1});
                    }
                    
                    //  2-1-2-2. (cy1, cx1)을 중심으로 회전하지 않을 경우
                    ny1 = cy1;
                    nx1 = cx1 + 1;
                    ny2 = cy1 + 1;
                    nx2 = cx1 + 1;
                    
                    if(!visited[ny1][nx1][ndir]) {  //  아직 방문하지 않은 경우
                        if(isEnd(ny1, nx1) || isEnd(ny2, nx2))
                            return ctime + 1;
                        
                        visited[ny1][nx1][ndir] = true;
                        queue.offer(new int[] {ny1, nx1, ndir, ctime + 1});
                    }
                }
            }
            //  2-2. 현재 상태가 수직한 상태인 경우
            else {
                //  2-2-1. 오른쪽으로 회전할 경우
                if(canMove(cy1, cx1 + 1) && canMove(cy1 + 1, cx1 + 1)) {    //  회전이 가능할 경우 (공간 확보됨)
                    //  2-2-1-1. (cy1, cx1)을 중심으로 회전할 경우
                    ny1 = cy1;
                    nx1 = cx1;
                    ny2 = cy1;
                    nx2 = cx1 + 1;
                    
                    if(!visited[ny1][nx1][ndir]) {  //  아직 방문하지 않은 경우
                        if(isEnd(ny1, nx1) || isEnd(ny2, nx2))
                            return ctime + 1;
                        
                        visited[ny1][nx1][ndir] = true;
                        queue.offer(new int[] {ny1, nx1, ndir, ctime + 1});
                    }
                    
                    //  2-2-1-2. (cy1, cx1)을 중심으로 회전하지 않을 경우
                    ny1 = cy1 + 1;
                    nx1 = cx1;
                    ny2 = cy1 + 1;
                    nx2 = cx1 + 1;
                    
                    if(!visited[ny1][nx1][ndir]) {  //  아직 방문하지 않은 경우
                        if(isEnd(ny1, nx1) || isEnd(ny2, nx2))
                            return ctime + 1;
                        
                        visited[ny1][nx1][ndir] = true;
                        queue.offer(new int[] {ny1, nx1, ndir, ctime + 1});
                    }
                }
                //  2-2-2. 왼쪽으로 회전할 경우
                if(canMove(cy1, cx1 - 1) && canMove(cy1 + 1, cx1 - 1)) {    //  회전이 가능할 경우 (공간 확보됨)
                    //  2-2-2-1. (cy1, cx1)을 중심으로 회전할 경우
                    ny1 = cy1;
                    nx1 = cx1 - 1;
                    ny2 = cy1;
                    nx2 = cx1;
                    
                    if(!visited[ny1][nx1][ndir]) {  //  아직 방문하지 않은 경우
                        if(isEnd(ny1, nx1) || isEnd(ny2, nx2))
                            return ctime + 1;
                        
                        visited[ny1][nx1][ndir] = true;
                        queue.offer(new int[] {ny1, nx1, ndir, ctime + 1});
                    }
                    
                    //  2-2-2-2. (cy1, cx1)을 중심으로 회전하지 않을 경우
                    ny1 = cy1 + 1;
                    nx1 = cx1 - 1;
                    ny2 = cy1 + 1;
                    nx2 = cx1;
                    
                    if(!visited[ny1][nx1][ndir]) {  //  아직 방문하지 않은 경우
                        if(isEnd(ny1, nx1) || isEnd(ny2, nx2))
                            return ctime + 1;
                        
                        visited[ny1][nx1][ndir] = true;
                        queue.offer(new int[] {ny1, nx1, ndir, ctime + 1});
                    }
                }
            }
        }
        
        return Integer.MAX_VALUE;   //  도착할 수 없을 경우
    }
    
    private int[] dy = {0, 1, 0, -1};
    private int[] dx = {1, 0, -1, 0};
    
    private boolean canMove(int y, int x) {
        return (0 <= y && y < N) && (0 <= x && x < N) && map[y][x] == 0;
    }
    
    private boolean isEnd(int y, int x) {
        return y == N - 1 && x == N - 1;
    }
}
```

### 프로그래머스 자동차 대여 기록 별 대여 금액 구하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/151141)

```sql
-- 코드를 입력하세요
select t3.history_id, round(t3.daily_fee * t3.length * (100 - coalesce(t4.discount_rate, 0)) / 100) fee
from
(
    select t1.daily_fee, t2.history_id, datediff(t2.end_date, t2.start_date) + 1 length,
        (
            case
                when 
                    datediff(t2.end_date, t2.start_date) + 1 >= 90
                then
                    2
                when
                    datediff(t2.end_date, t2.start_date) + 1 >= 30
                then
                    1
                when 
                    datediff(t2.end_date, t2.start_date) + 1 >= 7
                then
                    0
                else
                    -1
            end
        ) length_type
    from car_rental_company_car t1 inner join car_rental_company_rental_history t2
    on t1.car_id = t2.car_id
    where t1.car_type = '트럭'
) t3 left join
(
    select (
        case 
            when
                duration_type = '7일 이상'
            then
                0
            when
                duration_type = '30일 이상'
            then
                1
            else
                2
        end
    ) length_type, 
    discount_rate
    from car_rental_company_discount_plan
    where car_type = '트럭'
) t4
on t3.length_type = t4.length_type
order by fee desc, t3.history_id desc;
```