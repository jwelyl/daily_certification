# 25_03_05_daily_certification

# Problem Solving (Algorithm & SQL)

### 프로그래머스 등산코스 정하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/118669)

```java
import java.util.Arrays;
import java.util.List;
import java.util.ArrayList;
import java.util.PriorityQueue;

class Solution {
    private final long INF = 200_000L * 10_000_000 + 1;
    
    private int N;
    private boolean[] isGate;
    private boolean[] isSummit;
    
    private List<int[]>[] graph;
    
    private int[] answer = {-1, -1};
    
    public int[] solution(int n, int[][] paths, int[] gates, int[] summits) {
        N = n;
        isGate = new boolean[N + 1];
        isSummit = new boolean[N + 1];
        graph = new ArrayList[N + 1];
        for(int v = 0; v <= N; v++)
            graph[v] = new ArrayList<>();
    
        for(int gate : gates)
            isGate[gate] = true;
        
        for(int summit : summits)
            isSummit[summit] = true;
        
        for(int[] path : paths) {
            int v1 = path[0];
            int v2 = path[1];
            int c = path[2];
            
            if((isGate[v1] && isGate[v2]) || (isSummit[v1] && isSummit[v2]))
                continue;
            
            if(isGate[v1])
                graph[v1].add(new int[] {v2, c});
            else if(isGate[v2])
                graph[v2].add(new int[] {v1, c});
            else if(isSummit[v1])
                graph[v2].add(new int[] {v1, c});
            else if(isSummit[v2])
                graph[v1].add(new int[] {v2, c});
            else {
                graph[v1].add(new int[] {v2, c});
                graph[v2].add(new int[] {v1, c});
            }
        }

        parametricSearch(gates);
        
        return answer;
    }
    
    private void parametricSearch(int[] gates) {
        int start = 1;
        int end = 10_000_000;
        
        while(start <= end) {
            int mid = (start + end) / 2;
            
            int[] res = dijkstra(gates, mid);
            
            if(res[0] == -1)        //  최소 intensity mid로는 갈 수 없을 경우
                start = mid + 1;    //  최소 intensity를 더 키워야 함
            else {
                answer[0] = res[0];
                answer[1] = res[1]; //  일단 결과 저장
                end = mid - 1;
            }
        }
    }
    
    private int[] dijkstra(int[] gates, int limit) {
        PriorityQueue<long[]> pq = new PriorityQueue<>((n1, n2) -> Long.compare(n1[1], n2[1]));
        long[] dist = new long[N + 1];
        int[] res = {-1, -1};
        
        Arrays.fill(dist, INF);
        
        for(int gate : gates) {
            dist[gate] = 0;
            pq.offer(new long[] {gate, 0});
        }
        
        while(!pq.isEmpty()) {
            long[] cur = pq.poll();
            int cv = (int)cur[0];
            long cc = cur[1];

            // if(limit == 3)
            // System.out.println("cur = [" + cv + ", " + cc + "]");
            
            if(dist[cv] < cc)
                continue;
            
            for(int[] edge : graph[cv]) {
                int nv = edge[0];
                int cost = edge[1];
                
                if(cost > limit)
                    continue;
                
                long nc = cc + cost;
                
                if(nc < dist[nv]) {
                    dist[nv] = nc;
                    pq.offer(new long[] {nv, nc});
                    
                    if(isSummit[nv]) {
                        if(res[0] == -1) {
                            res[0] = nv;
                            res[1] = limit;
                        }
                        else if(nv < res[0])
                            res[0] = nv;
                    }
                }
            }
        }
        
        return res;
    }
}
```

### 프로그래머스 오프라인/온라인 판매 데이터 통합하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/131537)

```sql
-- 코드를 입력하세요
(select date_format(sales_date, '%Y-%m-%d') sales_date, product_id, user_id, sales_amount
from online_sale
where date_format(sales_date, '%Y-%m') = '2022-03')
union
(select date_format(sales_date, '%Y-%m-%d') sales_date, product_id, null user_id, sales_amount
from offline_sale
where date_format(sales_date, '%Y-%m') = '2022-03')
order by sales_date, product_id, user_id;
```