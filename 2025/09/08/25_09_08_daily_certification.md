# 25_09_08_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1175 배달

[1175번: 배달](http://boj.ma/1175/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.LinkedList;

public class Main {
    private static final int NONE = 4;    //    방향이 없음
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    
    private static char[][] map;
    
    private static int sy;
    private static int sx;
    private static int cy1 = -1;
    private static int cx1 = -1;
    private static int cy2 = -1;
    private static int cx2 = -1;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        map = new char[N][M];
        for(int y = 0; y < N; y++) {
            map[y] = br.readLine().toCharArray();
            for(int x = 0; x < M; x++) {
                if(map[y][x] == 'S') {
                    sy = y;
                    sx = x;
                }
                else if(map[y][x] == 'C') {
                    if(cy1 == -1 && cx1 == -1) {
                        cy1 = y;
                        cx1 = x;
                    }
                    else {
                        cy2 = y;
                        cx2 = x;
                    }
                }
            }
        }
        
        System.out.println(bfs());
    }    //    main-end
    
    private static int bfs() {
        Queue<Node> queue = new LinkedList<>();
        boolean[][][][] visited = new boolean[N][M][5][4];
        
        visited[sy][sx][NONE][0] = true;    //        시작 지점에서는 아무 방향으로나 이동 가능
        queue.offer(new Node(sy, sx, NONE, 0, 0));
        
        while(!queue.isEmpty()) {
            Node cur = queue.poll();
            int cy = cur.y;
            int cx = cur.x;
            int cd = cur.d;
            int cstatus = cur.status;
            int ccost = cur.cost;
            
            for(int nd = 0; nd < 4; nd++) {
                if(nd == cd)    //    연속으로 같은 방향 이동 불가
                    continue;
                
                int ny = cy + dy[nd];
                int nx = cx + dx[nd];
                int nstatus = cstatus;
                
                if(isIn(ny, nx) && map[ny][nx] != '#') {
                    if(map[ny][nx] == 'C') {    //    도착점일 경우
                        if(ny == cy1 && nx == cx1) {    //    첫번째 도착점일 경우
                            if(nstatus == 0 || nstatus == 2)
                                nstatus += 1;
                            
                            if(nstatus == 3)
                                return ccost + 1;
                            
                        }
                        else {    //    두번째 도착점일 경우
                            if(nstatus == 0 || nstatus == 1)
                                nstatus += 2;
                            
                            if(nstatus == 3)
                                return ccost + 1;
                        }
                    }
                    
                    if(!visited[ny][nx][nd][nstatus]) {
                        visited[ny][nx][nd][nstatus] = true;
                        queue.offer(new Node(ny, nx, nd, nstatus, ccost + 1));
                    }
                }
            }
        }
        
        return -1;
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < N) && (0 <= x && x < M);
    }
    
    private static class Node {
        public int y;
        public int x;
        public int d;
        public int status;    //    첫번째 배달 성공 시 1, 두번째 배달 성공 시 2, 둘 다 배달 시 3, 둘 다 배달 안했을 시 0
        public int cost;
        
        public Node(int y, int x, int d, int status, int cost) {
            this.y = y;
            this.x = x;
            this.d = d;
            this.status = status;
            this.cost = cost;
        }
    }
}    //    Main-class-end
```

### 오랜 기간 보호한 동물(1)

[](https://school.programmers.co.kr/learn/courses/30/lessons/59044)

```sql
select t1.name, t1.datetime
from animal_ins t1 left outer join animal_outs t2
on t1.animal_id = t2.animal_id
where t2.animal_id is null
order by t1.datetime asc;
```

### 대장균의 크기에 따라 분류하기 1

[](https://school.programmers.co.kr/learn/courses/30/lessons/299307)

```sql
select id, (
    case
        when size_of_colony <= 100 then 'LOW'
        when size_of_colony <= 1000 then 'MEDIUM'
        else 'HIGH'
    end
) as size
from ecoli_data
order by id;
```