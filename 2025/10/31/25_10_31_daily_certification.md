# 25_10_31_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16202 MST 게임

[16202번: MST 게임](http://boj.ma/16202/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;    //    정점 개수
    private static int M;    //    초기 간선 개수
    private static int K;    //    턴수
    
    //    edge : {v1, v2, cost}
    private static List<int[]> edges = new ArrayList<>();
    
    private static int[] parents;
    
    private static int[] answer;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        parents = new int[N + 1];
        answer = new int[K];
        
        for(int e = 1; e <= M; e++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            edges.add(new int[] {v1, v2, e});
        }
        
        for(int idx = 0; idx < K; idx++) {
            for(int v = 1; v <= N; v++)	//	분리집합 초기화
                parents[v] = v;
            
            if(!kruskal(idx))    //    더 이상 MST 만들 수 없을 경우
                break;
        }   
        
        for(int i = 0; i < K; i++)
            sb.append(answer[i]).append(" ");
        
        System.out.println(sb);
    }    //    main-end
    
    //    edges의 간선으로 MST를 만들 수 있으면 true 반환
    //    이전 kruskal때 start - 1번째 간선이 제외된 상태
    private static boolean kruskal(int start) {
        int mstCost = 0;
        int edgeCnt = 0;

        //    가장 작은 간선을 제외하고 MST 만들 수 있나 체크하기
        for(int i = start; i < edges.size(); i++) {
            int[] edge = edges.get(i);
            int v1 = edge[0];
            int v2 = edge[1];
            int cost = edge[2];
            
            if(find(v1) != find(v2)) {
                union(v1, v2);
                mstCost += cost;
                edgeCnt++;
                
                if(edgeCnt == N - 1)    //    MST가 완성된 경우
                    break;
            }
        }
        
        if(edgeCnt < N - 1)    //    MST가 완성되지 않은 경우
            return false;
        
        answer[start] = mstCost;
        
        return true;
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
}    //    Main-class-end
```