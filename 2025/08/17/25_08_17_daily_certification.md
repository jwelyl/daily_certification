# 25_08_17_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 3197 백조의 호수

[3197번: 백조의 호수](http://boj.ma/3197/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.LinkedList;

public class Main {
    private static final char VISITED = '0';
    private static final char WATER = '.';
    private static final char ICE = 'X';
    private static final char SWAN = 'L';
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int R;    //    행 크기
    private static int C;    //    열 크기
    
    private static char[][] map;
    
    private static int swanNum1 = -1;
    private static int swanNum2 = -1;
    
    private static int[] parents;
    
    private static final Queue<Pos> queue1 = new LinkedList<>();
    private static final Queue<Pos> queue2 = new LinkedList<>();
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        R = Integer.parseInt(st.nextToken());
        C = Integer.parseInt(st.nextToken());
        
        map = new char[R][C];
        parents = new int[R * C];
        for(int r = 0; r < R; r++) {
            map[r] = br.readLine().toCharArray();
            for(int c = 0; c < C; c++) {
                if(map[r][c] == SWAN) {
                    if(swanNum1 == -1)
                        swanNum1 = r * C + c;
                    else
                        swanNum2 = r * C + c;
                }
                
                parents[r * C + c] = r * C + c;
            }
        }
        
        if(swanNum1 == -1 || swanNum2 == -1) {    //    백조가 한마리 이하일 경우
            System.out.println(0);
            return;
        }
                    
        init();
        
        System.out.println(bfs2());
    }    //    main-end
    
    private static void init() {
        for(int y = 0; y < R; y++) {
            for(int x = 0; x < C; x++) {
                if(map[y][x] == WATER || map[y][x] == SWAN)    //    물 또는 백조를 발견할 경우
                    bfs1(y, x);    //    하나의 물 집합으로 만듬
            }
        }
    }
    
    private static void bfs1(int y, int x) {
        map[y][x] = VISITED;
        queue1.offer(new Pos(y, x));
        queue2.offer(new Pos(y, x));
        
        while(!queue1.isEmpty()) {
            Pos cur = queue1.poll();
            int cy = cur.y;
            int cx = cur.x;
            int cnum = cy * C + cx;
            
            for(int d = 0; d < 4; d++) {
                int ny = cy + dy[d];
                int nx = cx + dx[d];
                
                if(isIn(ny, nx) && (map[ny][nx] == WATER || map[ny][nx] == SWAN)) {
                    int nnum = ny * C + nx;
                    map[ny][nx] = VISITED;
                    queue1.offer(new Pos(ny, nx));
                    queue2.offer(new Pos(ny, nx));
                    union(cnum, nnum);
                }
            }
        }
    }
    
    private static int bfs2() {
        int time = 0;
        
        if(find(swanNum1) == find(swanNum2))    //    처음부터 두 백조가 하나의 물 덩어리에 있었을 경우
            return time;
        
        WHILE:
        while(!queue2.isEmpty()) {
            int size = queue2.size();
            
            for(int i = 0; i < size; i++) {
                Pos cur = queue2.poll();
                int cy = cur.y;
                int cx = cur.x;
                int cnum = cy * C + cx;
                
                for(int d = 0; d < 4; d++) {
                    int ny = cy + dy[d];
                    int nx = cx + dx[d];
                    
                    if(isIn(ny, nx) && map[ny][nx] == ICE) {    //    얼음이 있는 칸일 경우
                        int nnum = ny * C + nx;
                        map[ny][nx] = VISITED;
                        queue2.offer(new Pos(ny, nx));
                        union(cnum, nnum);    //    얼음이 녹으면서 물이 있는 칸이 확장됨
                        
                        for(int dir = 0; dir < 4; dir++) {    //    확장된 칸 주변 4칸 확인
                            int nny = ny + dy[dir];
                            int nnx = nx + dx[dir];
                            
                            //    확장된 칸 주변에 물 집합이거나, 얼음이 녹아서 물이 된 칸이 있을 경우
                            if(isIn(nny, nnx) && map[nny][nnx] == VISITED) {
                                int nnnum = nny * C + nnx;
                                union(nnum, nnnum);    //    그 칸과 확장된 칸을 같은 물 집합으로 합치기
                                
                                if(find(swanNum1) == find(swanNum2)) {    //    합쳤을때 두 백조가 연결될 경우
                                    time++;
                                    break WHILE;
                                }
                            }
                        }
                    }
                }
            }
            
            time++;
        }
        
        return time;
    }
    
    private static int find(int v) {
        return v == parents[v] ? v : (parents[v] = find(parents[v]));
    }
    
    private static void union(int v1, int v2) {
        v1 = find(v1);
        v2 = find(v2);
        
        if(v1 != v2)
            parents[v2] = v1;
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < R) && (0 <= x && x < C);
    }
    
    private static class Pos {
        public int y;
        public int x;
        
        public Pos(int y, int x) {
            this.y = y;
            this.x = x;
        }
    }
}    //    Main-class-end
```