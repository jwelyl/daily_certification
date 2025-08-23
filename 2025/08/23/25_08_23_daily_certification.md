# 25_08_23_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 25545 Liquid Cats

[23545번: Liquid Cats](http://boj.ma/23545/t)

**BFS + Parametric Search (MLE)**

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.LinkedList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    private static int K;
    
    private static char[][] map;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        map = new char[N][M];
        for(int y = 0; y < N; y++)
            map[y] = br.readLine().toCharArray();
        
        System.out.println(parametricSearch());
    }    //    main-end
    
    private static int parametricSearch() {
        int start = 0;
        int end = N - 1;
        int res = start;
        
        while(start <= end) {
            int mid = (start + end) / 2;
            
            if(ok(mid)) {
                res = mid;
                start = mid + 1;
            }
            else
                end = mid - 1;
        }
        
        return res + 1;
    }
    
    private static boolean ok(int yLimit) {
        boolean[][] visited = new boolean[N][M];
        
        for(int y = N - 1; y >= yLimit; y--) {
            for(int x = 0; x < M; x++) {
                if(map[y][x] == '.' && !visited[y][x]) {
                    int cnt = bfs(y, x, yLimit, visited);
                    
                    if(cnt >= K)
                        return true;
                }
            }
        }
        
        return false;
    }
    
    private static int bfs(int y, int x, int yLimit, boolean[][] visited) {
        Queue<int[]> queue = new LinkedList<>();
        int res = 1;
        visited[y][x] = true;
        queue.offer(new int[] {y, x});
        
        while(!queue.isEmpty()) {
            int[] cur = queue.poll();
            int cy = cur[0];
            int cx = cur[1];
            
            for(int d = 0; d < 4; d++) {
                int ny = cy + dy[d];
                int nx = cx + dx[d];
                
                if(isIn(ny, nx, yLimit) && map[ny][nx] == '.' && !visited[ny][nx]) {
                    res++;
                    visited[ny][nx] = true;
                    queue.offer(new int[] {ny, nx});
                }
            }
        }
        
        return res;
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x, int yLimit) {
        return (yLimit <= y && y < N) && (0 <= x && x < M);
    }
}    //    Main-class-end
```

**Disjoint Set + Parametric Search (MLE)**

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final int NONE = Integer.MIN_VALUE;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    private static int K;
    
    private static char[][] map;
    
    private static int[] parents;
    private static int[] dsSizes;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        map = new char[N][M];
        parents = new int[N * M];
        dsSizes = new int[N * M];
        
        for(int y = 0; y < N; y++)
            map[y] = br.readLine().toCharArray();
        
        System.out.println(parametricSearch());
    }    //    main-end
    
    private static int parametricSearch() {
        int start = 0;
        int end = N - 1;
        int res = NONE;
        
        while(start <= end) {
            int mid = (start + end) / 2;
            
            reset();
            
            if(ok(mid)) {
                res = mid;
                start = mid + 1;
            }
            else
                end = mid - 1;
        }
        
        return res == NONE ? -1 : res + 1;
    }
    
    private static boolean ok(int yLimit) {
        for(int cy = N - 1; cy >= yLimit; cy--) {
            for(int cx = 0; cx < M; cx++) {
                if(map[cy][cx] == '.') {
                    int cnum = cy * M + cx;
                    
                    for(int d = 0; d < 4; d++) {
                        int ny = cy + dy[d];
                        int nx = cx + dx[d];
                        
                        if(isIn(ny, nx, yLimit) && map[ny][nx] == '.') {
                            int nnum = ny * M + nx;
                            union(cnum, nnum);
                        }
                    }
                
                    if(dsSizes[find(cnum)] >= K)
                        return true;
                }
            }
        }
        
        return false;
    }
    
    private static void reset() {
        for(int v = 0; v < N * M; v++) {
            parents[v] = v;
            dsSizes[v] = 1;
        }
    }
    
    private static int find(int v) {
        return v == parents[v] ? v : (parents[v] = find(parents[v]));
    }
    
    private static void union(int v1, int v2) {
        v1 = find(v1);
        v2 = find(v2);
        
        if(v1 != v2) {
            parents[v2] = v1;
            dsSizes[v1] += dsSizes[v2];
            dsSizes[v2] = 0;
        }
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x, int yLimit) {
        return (yLimit <= y && y < N) && (0 <= x && x < M);
    }
}    //    Main-class-end
```

**Disjoint Set**

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    private static int K;
    
    private static char[][] map;
    
    private static int[] parents;
    private static int[] dsSizes;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        map = new char[N][M];
        parents = new int[N * M];
        dsSizes = new int[N * M];
        
        for(int y = 0; y < N; y++) {
            map[y] = br.readLine().toCharArray();
            for(int x = 0; x < M; x++) {
            	parents[y * M + x] = y * M + x;
            	dsSizes[y * M + x] = 1;
            }
        }
            
        for(int y = N - 1; y >= 0; y--) {
        	for(int x = 0; x < M; x++) {
        		if(map[y][x] == '.') {
	        		int cnum = y * M + x;
	        		
	        		for(int d = 0; d < 2; d++) {	//	오른쪽, 아래 칸과 합칠 수 있는지 확인
	        			int ny = y + dy[d];
	        			int nx = x + dx[d];
	        			
	        			if(isIn(ny, nx) && map[ny][nx] == '.') {
	        				int nnum = ny * M + nx;
	        				union(cnum, nnum);
	        			}
	        		}
                    
	        		if(dsSizes[find(cnum)] >= K) {
	        			System.out.println(y + 1);
	        			return;
	        		}
        		}
        	}
        }
        
        System.out.println(-1);
    }    //    main-end
    private static int find(int v) {
        return v == parents[v] ? v : (parents[v] = find(parents[v]));
    }
    
    private static void union(int v1, int v2) {
        v1 = find(v1);
        v2 = find(v2);
        
        if(v1 != v2) {
            parents[v2] = v1;
            dsSizes[v1] += dsSizes[v2];
            dsSizes[v2] = 0;
        }
    }
    
    private static final int[] dy = {0, 1};
    private static final int[] dx = {1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < N) && (0 <= x && x < M);
    }
}    //    Main-class-end
```

### BOJ 16974 레벨 햄버거

[16974번: 레벨 햄버거](http://boj.ma/16974/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static long X;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        X = Long.parseLong(st.nextToken());
        
        System.out.println(recursion(N, X));
    }    //    main-end
    
    private static long recursion(int n, long x) {
        if(x == 0)
            return 0;
        if(n == 0)    //    레벨 0 햄버거는 패티 1장만 존재
            return 1;
        
        //    레벨 n - 1 햄버거의 장 수
        long total = (long)Math.pow(2, (n - 1) + 2) - 3;
        //    레벨 n - 1 햄버거에 존재하는 패티 수
        long patties = (long)Math.pow(2, (n - 1) + 1) - 1;
        
        long res = 0;
        
        //    레벨 n 햄버거 = 번 + (레벨 n - 1 햄버거) + 패티 + (레벨 n - 1 햄버거) + 번
        if(x == 1)        //    위의 1장만 먹을 경우, 번 1장임
            res = 0;      //    패티 0장
        else if(1 < x && x <= 1 + total)            //    위에 있는 레벨 n - 1 햄버거까지만 먹는 경우
            res = recursion(n - 1, x - 1);          //    레벨 n - 1 햄버거에서 위의 x - 1 장을 먹는 것과 같음
        else {
            res = patties + 1;    //    위에 있는 레벨 n - 1 햄버거에 있는 패티와 중간에 있는 패티 한 장은 모두 먹음
            res += recursion(n - 1, x - (1 + total + 1));     //    아래에 있는 다른 레벨 n - 1 햄버거부터 추가로 먹음
        }
        
        return res;
    }
}    //    Main-class-end
```