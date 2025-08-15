# 25_08_15_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 6091 핑크 플로이드

[6091번: 핑크 플로이드](http://boj.ma/6091/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Collections;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;
    
    private static final List<Edge> edgeList = new ArrayList<>();
    private static final List<Edge> treeEdgeList = new ArrayList<>();
    
    private static int[] parents;
    private static List<Integer>[] tree;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        tree = new ArrayList[N + 1];
        parents = new int[N + 1];
        for(int v = 0; v <= N; v++) {
            tree[v] = new ArrayList<>();
            parents[v] = v;
        }
        
        for(int v1 = 1; v1 < N; v1++) {
            st = new StringTokenizer(br.readLine());
            for(int v2 = v1 + 1; v2 <= N; v2++) {
                int cost = Integer.parseInt(st.nextToken());
                edgeList.add(new Edge(v1, v2, cost));
            }
        }
        
        kruskal();
        
        for(Edge edge : treeEdgeList) {
        	int v1 = edge.v1;
    		int v2 = edge.v2;
    		
    		tree[v1].add(v2);
    		tree[v2].add(v1);
        }
        
        for(int v = 1; v <= N; v++) {
        	Collections.sort(tree[v]);
        	sb.append(tree[v].size()).append(" ");
        	for(int nv : tree[v])
        		sb.append(nv).append(" ");
        	sb.append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static void kruskal() {
    	int edgeCnt = 0;
    	
    	Collections.sort(edgeList);
    	
    	for(Edge  edge : edgeList) {
    		int v1 = edge.v1;
    		int v2 = edge.v2;
    		
    		v1 = find(v1);
    		v2 = find(v2);
    		
    		if(v1 != v2) {
    			edgeCnt++;
    			union(v1, v2);
    			treeEdgeList.add(edge);
    			
    			if(edgeCnt == N - 1)
    				break;
    		}
    	}
    }
    
    private static int find(int v) {
    	return v == parents[v] ? v : (parents[v] = find(parents[v]));
    }
    
    private static void union(int v1, int v2) {
    	parents[v2] = v1;
    }
    
    private static class Edge implements Comparable<Edge> {
    	public int v1;
    	public int v2;
    	public int cost;
    	
    	public Edge(int v1, int v2, int cost) {
    		this.v1 = v1;
    		this.v2 = v2;
    		this.cost = cost;
    	}

		@Override
		public int compareTo(Edge other) {
			return Integer.compare(this.cost, other.cost);
		}
    }
}    //    Main-class-end
```

### BOJ 24041 성싶당 밀키트

[24041번: 성싶당 밀키트](http://boj.ma/24041/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Collections;

public class Main {
    private static final int MAX = 2_000_000_000;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    재료 수
    private static int G;    //    허용 가능한 세균 수
    private static int K;    //    제외할 수 있는 중요하지 않은 재료 수
    
    private static int[] sArr;        //    sArr[i] : i번째 재료의 부패 속도
    private static int[] lArr;        //    lArr[i] : i번째 재료의 유통기한
    private static boolean[] oArr;    //    oArr[i] : i번째 재료가 중요하면 true
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        G = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        sArr = new int[N];
        lArr = new int[N];
        oArr = new boolean[N];
        
        for(int i = 0; i < N; i++) {
            st = new StringTokenizer(br.readLine());
            int s = Integer.parseInt(st.nextToken());
            int l = Integer.parseInt(st.nextToken());
            int o = Integer.parseInt(st.nextToken());
            
            sArr[i] = s;
            lArr[i] = l;
            if(o == 0)    //    중요한 재료면
                oArr[i] = true;
        }
        
        System.out.println(parametricSearch());
    }    //    main-end
    
    private static int parametricSearch() {
        int start = 0;
        int end = MAX;
        int res = 0;    //    구매 후 최대 res일까지 먹을 수 있음
        
        while(start <= end) {
            int mid = start + (end - start) / 2;    //    최대 mid일 지난 후에 먹을 수 있는지 확인
            
            if(ok(mid)) {                           //    mid일 지난 후에도 먹을 수 있을 경우
                res = mid;                          //    일단 mid 저장
                start = mid + 1;                    //    더 오랜 시간 지난 것도 먹을 수 있는지 확인
            }
            else                                    //    mid일 지난 후에는 먹을 수 없다면
                end = mid - 1;                      //    더 적은 시간 확인   
        }
        
        return res;
    }
    
    //    day일 지난 후에 먹을 수 있나면 true
    private static boolean ok(int day) {
        long sum = 0;
        
        List<Long> list = new ArrayList<>();    //    뺄 수 있는 재료들에 의해 생기는 세균 수 리스트
        
        for(int i = 0; i < N; i++) {
            if(oArr[i])    //    중요한 재료일 경우
                sum += (long)sArr[i] * Math.max(1, day - lArr[i]);
            else    //    중요하지 않은 재료일 경우
                list.add((long)sArr[i] * Math.max(1, day - lArr[i]));
        }
        
        Collections.sort(list, Collections.reverseOrder());    //    세균 수 많은 순으로 정렬
        
        for(int i = 0; i < list.size(); i++) {
            if(i < K)        //    세균이 가장 많이 생기는 K개의 재료 제거
                continue;
            
            sum += list.get(i);
        }
        
        return sum <= G;
    }
}    //    Main-class-end
```