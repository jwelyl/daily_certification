# 24_10_09_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 9205 맥주 마시면서 걸어가기

[](https://www.acmicpc.net/problem/9205)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.List;
import java.util.LinkedList;
import java.util.ArrayList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int T;    //    테스트 케이스 개수
    private static int N;    //    편의점 개수
    
    private static int[] xpos;
    private static int[] ypos;    //    좌표
    
    private static List<Integer>[] graph;    //    그래프
    
    public static void main(String[] args) throws IOException {
        T = Integer.parseInt(br.readLine());
        
        for(int tc = 1; tc <= T; tc++) {
            N = Integer.parseInt(br.readLine());
            
            xpos = new int[N + 2];
            ypos = new int[N + 2];
            graph = new ArrayList[N + 2];
            
            for(int i = 0; i < N + 2; i++)
                graph[i] = new ArrayList<>();
            
            for(int i = 0; i < N + 2; i++) {
                st = new StringTokenizer(br.readLine());
                xpos[i] = Integer.parseInt(st.nextToken());
                ypos[i] = Integer.parseInt(st.nextToken());
            
                for(int j = 0; j < i; j++) {
                    if(canGo(i, j)) {
                        graph[i].add(j);
                        graph[j].add(i);
                    }
                }
            }
            
            sb.append(bfs());
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static String bfs() {
        Queue<Integer> q = new LinkedList<>();
        boolean[] visited = new boolean[N + 2];
        
        visited[0] = true;
        q.offer(0);
        
        while(!q.isEmpty()) {
            int cur = q.poll();
            
            for(int next : graph[cur]) {
                if(next == N + 1)
                    return "happy\n";
                
                if(!visited[next]) {
                    visited[next] = true;
                    q.offer(next);
                }
            }
        }
        
        return "sad\n";
    }
    
    private static boolean canGo(int v1, int v2) {
        return Math.abs(xpos[v1] - xpos[v2]) + Math.abs(ypos[v1] - ypos[v2]) <= 1_000;
    }
}    //    Main-class-end
```

### BOJ 7662 이중 우선순위 큐

[](https://www.acmicpc.net/problem/7662)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.Map.Entry;
import java.util.StringTokenizer;
import java.util.TreeMap;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    //    K ; PQ에 저장된 수, V : 저장된 수의 개수
    private static final TreeMap<Integer, Integer> pq = new TreeMap<>();
    
    private static int T;    //    테스트케이스 개수
    private static int K;    //    쿼리 수
    
    public static void main(String[] args) throws IOException {
        T = Integer.parseInt(br.readLine());
        
        for(int tc = 1; tc <= T; tc++) {
            K = Integer.parseInt(br.readLine());
            
            pq.clear();
            
            for(int k = 1; k <= K; k++) {
                st = new StringTokenizer(br.readLine());
                
                String cmd = st.nextToken();
                
                switch(cmd) {
                    case "I":
                        int num = Integer.parseInt(st.nextToken());
                        pq.put(num, pq.getOrDefault(num, 0) + 1);
                        break;
                    case "D":
                        if(pq.isEmpty())
                            continue;
                        
                        int opt = Integer.parseInt(st.nextToken());
                        
                        if(opt == -1) {   //    최솟값 삭제
                            int firstKey = pq.firstKey();
                            int cnt = pq.get(firstKey);
                            
                            if(cnt == 1)
                            	pq.remove(firstKey);
                            else
                            	pq.put(firstKey, cnt - 1);
                            
                        }
                        else {            //    최댓값 삭제                            
                            int lastKey = pq.lastKey();
                            int cnt = pq.get(lastKey);
                            
                            if(cnt == 1)
                            	pq.remove(lastKey);
                            else
                            	pq.put(lastKey, cnt - 1);
                        }
                }
            }
            
            if(pq.isEmpty())
                sb.append("EMPTY\n");
            else
                sb.append(pq.lastKey()).append(" ").append(pq.firstKey()).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
}    //    Main-class-end
```