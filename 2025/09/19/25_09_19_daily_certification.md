# 25_09_19_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1045 도로

[1045번: 도로](http://boj.ma/1045/t)

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
    
    private static int N;    //    도시 개수
    private static int M;    //    목표하는 도로 집합 크기
    
    private static char[][] graph;    //    도시 간 연결 상태
    private static int[] parents;
    
    private static final List<Edge> edgeList = new ArrayList<>();
    
    private static int[] cnts;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        if(M < N - 1) {    //    N - 1개보다 적은 도로로는 연결된 집합을 만들 수 없음
            System.out.println(-1);
            return;
        }
        
        graph = new char[N][N];
        parents = new int[N];
        cnts = new int[N];
        
        for(int v1 = 0; v1 < N; v1++) {
            graph[v1] = br.readLine().toCharArray();
            parents[v1] = v1;
            
            for(int v2 = v1 + 1; v2 < N; v2++) {
                if(graph[v1][v2] == 'Y')
                    edgeList.add(new Edge(v1, v2));
            }
        }
        
        kruskal();
    }    //    main-end
    
    private static void kruskal() {
        int edgeCnt = 0;
        List<Edge> edgeSet = new ArrayList<>();
        
        //    일단 MST를 만들어 연결상태 만들기
        for(Edge edge : edgeList) {
            int v1 = edge.v1;
            int v2 = edge.v2;
            
            if(find(v1) != find(v2)) {
                union(v1, v2);
                edgeCnt++;
                edge.used = true;
                edgeSet.add(edge);
                
                if(edgeCnt == N - 1)
                    break;
            }
        }
        
        if(edgeCnt < N - 1) {   //    연결 상태를 만들지 못할 경우
            System.out.println(-1);
            return;
        }
        
        //    이미 연결된 상태에서 M개 도로 채우기 위해 추가 도로 확인
        if(edgeCnt < M) {
            for(Edge edge : edgeList) {
                if(edge.used)
                    continue;
                
                edgeSet.add(edge);
                edgeCnt++;
                
                if(edgeCnt == M)
                    break;
            }
        }
        
        if(edgeCnt < M)
            System.out.println(-1);
        else {
            for(Edge edge : edgeSet) {
                int v1 = edge.v1;
                int v2 = edge.v2;
                
                cnts[v1]++;
                cnts[v2]++;
            }
            
            for(int i = 0; i < N; i++)
                sb.append(cnts[i]).append(" ");
        
            System.out.println(sb);
        }
    }
    
    private static int find(int v) {
        return v == parents[v] ? v : (parents[v] = find(parents[v]));
    }
    
    private static void union(int v1, int v2) {
        v1 = find(v1);
        v2 = find(v2);
        
        if(v1 != v2)
            parents[v1] = v2;
    }
    
    private static class Edge {
        public int v1;
        public int v2;
        public boolean used;
        
        public Edge(int v1, int v2) {
            this.v1 = v1;
            this.v2 = v2;
            this.used = false;
        }
    }
}    //    Main-class-end
```