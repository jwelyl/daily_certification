# 25_09_05_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16137 견우와 직녀

[16137번: 견우와 직녀](http://boj.ma/16137/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.LinkedList;

public class Main {
    private static final int HOLE = 0;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    행/열 크기
    private static int M;    //    놓을 수 있는 오작교의 주기
    
    private static int[][] map;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        map = new int[N][N];
        for(int y = 0; y < N; y++) {
            st = new StringTokenizer(br.readLine());
            for(int x = 0; x < N; x++)
                map[y][x] = Integer.parseInt(st.nextToken());
        }
        
        System.out.println(bfs());
    }    //    main-end
    
    private static int bfs() {
        Queue<Node> queue = new LinkedList<>();
        boolean[][][] visited = new boolean[N][N][2];
        visited[0][0][1] = true;
        queue.offer(new Node(0, 0, 1, 0));
        
        while(!queue.isEmpty()) {
            Node cur = queue.poll();
            int cy = cur.y;
            int cx = cur.x;
            int cr = cur.remain;
            int ct = cur.time;
            
            for(int d = 0; d < 4; d++) {
                int ny = cy + dy[d];
                int nx = cx + dx[d];
                int nr = cr;
                int nt = ct + 1;
                
                if(isIn(ny, nx)) {
                    if(ny == N - 1 && nx == N - 1)    //    도착한 경우
                        return nt;
                
                    if(map[ny][nx] == 1) {    //    지나갈 수 있는 땅인 경우
                        if(!visited[ny][nx][nr]) {
                            visited[ny][nx][nr] = true;
                            queue.offer(new Node(ny, nx, nr, nt));
                        }
                    }
                    else if(map[ny][nx] != HOLE) {    //    이미 놓여져 있는 오작교일 경우
                    	if(map[cy][cx] == 1)	{	//	이미 (cy, cx)가 오작교면 다음엔 오작교 건널 수 없음
                    		if(nt % map[ny][nx] == 0) {    //    현재 그 오작교를 건널 수 있을 경우
                            	if(!visited[ny][nx][nr]) {
                                	visited[ny][nx][nr] = true;
                                	queue.offer(new Node(ny, nx, nr, nt));
                            	}
                        	}
                        	else	//	현재 그 오작교를 건널 수 없는 경우
                        		queue.offer(new Node(cy, cx, nr, nt));
                    	}
                    }
                    else {    //    절벽일 경우
                        if(!isCross(ny, nx) && nr == 1 && map[cy][cx] == 1) {    //    오작교를 놓을 수 있는 절벽이고 오작교가 남았을 경우
                            if(nt % M == 0) {     //    오작교를 놓으면 바로 건널 수 있을 경우
                                if(!visited[ny][nx][0]) {
                                    visited[ny][nx][0] = true;   //    오작교 1개 소모
                                    queue.offer(new Node(ny, nx, 0, nt));
                                }
                            }
                            else    //    오작교를 놓아도 바로 건널 수 없어서 기다려야 할 경우
                                queue.offer(new Node(cy, cx, nr, nt));    
                        }
                    }
                }
            }
        }
        
        return -1;
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < N) && (0 <= x && x < N);
    }
    
    private static boolean isCross(int y, int x) {
        for(int d = 0; d < 4; d++) {
            int y1 = y + dy[d];
            int x1 = x + dx[d];
            int y2 = y + dy[(d + 1) % 4];
            int x2 = x + dx[(d + 1) % 4];
            
            if(isIn(y1, x1) && isIn(y2, x2) && map[y1][x1] == HOLE && map[y2][x2] == HOLE)
                return true;
        }
        
        return false;
    }
    
    private static class Node {
        public int y;
        public int x;
        public int remain;    //    주기 M인 오작교를 한번 놓을 수 있는 횟수
        public int time;      //    (y, x)에 오작교 놓을 수 있는 횟수가 remain인 상태로 오는데 걸린 시간
        
        public Node(int y, int x, int remain, int time) {
            this.y = y;
            this.x = x;
            this.remain = remain;
            this.time = time;
        }
    }
}    //    Main-class-end
```

### 대장균의 크기에 따라 분류하기 2

[](https://school.programmers.co.kr/learn/courses/30/lessons/301649)

```sql
select t.id as id,
(
    case
        when t.per_rank <= 0.25 then 'CRITICAL'
        when t.per_rank <= 0.50 then 'HIGH'
        when t.per_rank <= 0.75 then 'MEDIUM'
        else 'LOW'
    end
) as colony_name
from (
    select
        id,
        percent_rank() over (order by size_of_colony desc) per_rank
    from ecoli_data
) t
order by t.id;
```