# 24_09_19_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2493 탑

[](https://www.acmicpc.net/problem/2493)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.Stack;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;    //    탑 개수
    private static final Stack<int[]> stack = new Stack<>();
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        st = new StringTokenizer(br.readLine());
        for(int i = 1; i <= N; i++) {
            int h = Integer.parseInt(st.nextToken());    //    i번째 탑 높이
            int bNum = 0;    //    i번째 탑보다 높이가 높은 이전 탑 중 가장 먼저 만나는 탑의 번호, 없으면 0
            
            while(!stack.isEmpty()) {    //    이전 탑이 존재할 경우
                int[] before = stack.peek();    //    이전 탑
                
                if(before[1] > h) {    //    이전 탑의 높이가 현재 탑보다 높을 경우
                    bNum = before[0];
                    break;
                }
                else stack.pop();    //    i번째 탑보다 높이가 작은 이전 탑은 스택에서 제거
            }
            
            sb.append(bNum).append(" ");
            stack.push(new int[] {i, h});
        }
        
        System.out.println(sb);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 2412 암벽 등반 (BFS, HashMap)

[](https://www.acmicpc.net/problem/2412)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.Map;
import java.util.HashMap;
import java.util.Queue;
import java.util.LinkedList;
import java.util.StringTokenizer;

public class Main {
    private static final int MAX_X = 1_000_000;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    홈 개수
    private static int T;    //    정상 높이
    
    private static Map<Integer, Integer>[] graph;    //    graph[y] : y좌표가 y인 홈의 Map, Key : x좌표, Value : 홈 번호
    private static boolean[] visited;    //    홈 방문 처리 배열, visited[nth] : nth번째 홈 방문 시 true
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        T = Integer.parseInt(st.nextToken());
        
        graph = new HashMap[T + 1];
        for(int y = 0; y <= T; y++)
            graph[y] = new HashMap<>();
        
        visited = new boolean[N + 1];
        
        for(int num = 1; num <= N; num++) {
            st = new StringTokenizer(br.readLine());
            int x = Integer.parseInt(st.nextToken());
            int y = Integer.parseInt(st.nextToken());
            
            graph[y].put(x, num);    //    높이 y에 x좌표가 x인 홈 num을 저장
        }
        
        System.out.println(bfs());
    }    //    main-end
    
    private static int bfs() {
        Queue<int[]> q = new LinkedList<>(); //    {x, y, time} 저장
        q.offer(new int[] {0, 0, 0});    //    (0, 0)에서 출발
        
        while(!q.isEmpty()) {
            int[] cur = q.poll();
            int cx = cur[0];
            int cy = cur[1];        //    현재 좌표 (cy, cx)
            int ctime = cur[2];     //    (cy, cx)까지 오는데 걸리는 시간
            
            for(int ny = Math.max(0, cy - 2); ny <= Math.min(T, cy + 2); ny++) {    //    cy에서 갈 수 있는 y좌표 ny
                for(int nx = Math.max(0, cx - 2); nx <= Math.min(MAX_X, cx + 2); nx++) {    //    cx에서 갈 수 있는 x좌표 nx
                    if(!graph[ny].containsKey(nx))    //    (ny, nx)에 해당하는 홈이 없을 경우
                        continue;
                    
                    if(ny == T)    //    정상에 도달했을 경우
                        return ctime + 1;
                    
                    int num = graph[ny].get(nx);    //    (ny, nx)에 해당하는 홈 번호
                    
                    if(visited[num])    //    해당 홈을 이미 방문했을 경우
                        continue;
                    
                    visited[num] = true;    //    해당 홈 방문 처리
                    q.offer(new int[] {nx, ny, ctime + 1});
                }
            }
        }
        
        return -1;    //    높이 T에 도달할 수 없을 경우
    }    //    bfs-end
}    //    Main-class-end
```