# 25_11_03_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 25498 핸들 뭘로 하지

[25498번: 핸들 뭘로 하지](http://boj.ma/25498/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Queue;
import java.util.ArrayDeque;

public class Main {
    private static final int ROOT = 0;
    private static final char NONE = '\0';
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;                    //    정점 개수
    private static char[] chArr;             //    chArr[num] : num번째 정점의 문자 값
    private static List<Integer>[] tree;
    
    private static final StringBuilder answer = new StringBuilder();
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        chArr = br.readLine().toCharArray();
        tree = new ArrayList[N];
        for(int v = 0; v < N; v++)
            tree[v] = new ArrayList<>();
        
        for(int e = 0; e < N - 1; e++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken()) - 1;
            int v2 = Integer.parseInt(st.nextToken()) - 1;
            
            tree[v1].add(v2);
            tree[v2].add(v1);
        }
        
        bfs();
        
        System.out.println(answer);
    }    //    main-end
    
    private static void bfs() {
        Queue<Integer> queue = new ArrayDeque<>();
        boolean[] visited = new boolean[N];
        
        visited[ROOT] = true;
        answer.append(chArr[ROOT]);
        queue.offer(ROOT);
        
        while(!queue.isEmpty()) {
            int size = queue.size();    //    현재 깊이에 해당하는 노드 수
            
            char max = NONE;    //    현재 깊이의 자식 노드의 값 중 가장 큰 값
            for(int i = 0; i < size; i++) {
                int cur = queue.poll();
                
                for(int next : tree[cur]) {
                    if(!visited[next] && chArr[next] > max)
                        max = chArr[next];
                }
                
                queue.offer(cur);    //    bfs를 위해 다시 복구
            }
            
            if(max != NONE)
                answer.append(max);
            
            for(int i = 0; i < size; i++) {
                int cur = queue.poll();
                
                for(int next : tree[cur]) {
                    if(chArr[next] == max && !visited[next]) {
                        visited[next] = true;
                        queue.offer(next);
                    }
                }
            }
        }
    }
}    //    Main-class-end
```