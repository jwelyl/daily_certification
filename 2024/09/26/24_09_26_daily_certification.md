# 24_09_26_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 15971 두 로봇

[](https://www.acmicpc.net/problem/15971)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.List;
import java.util.ArrayList;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    정점 개수
    private static int V1;   //    정점 1
    private static int V2;   //    정점 2
    
    private static List<Edge>[] tree;
    private static List<Edge>[] path;
    private static int[] dist;
    private static boolean[] visited;
    
    private static int maxEdge = Integer.MIN_VALUE;    //    V1, V2 사이 경로를 이루는 간선 비용 중 가장 큰 간선 비용
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        V1 = Integer.parseInt(st.nextToken());
        V2 = Integer.parseInt(st.nextToken());
        
        if(V1 == V2) {
        	System.out.println(0);
        	return;
        }
        
        tree = new ArrayList[N + 1];
        path = new ArrayList[N + 1];
        for(int i = 1; i <= N; i++) {
            tree[i] = new ArrayList<>();
            path[i] = new ArrayList<>();
        }
        
        for(int m = 0; m < N - 1; m++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int cost = Integer.parseInt(st.nextToken());
            tree[v1].add(new Edge(v2, cost));
            tree[v2].add(new Edge(v1, cost));
        }
        
        dist = new int[N + 1];
        visited = new boolean[N + 1];
        
        dfs(V1, 0);
        
        int cur = V1;
        while(!path[cur].isEmpty()) {
            int next = path[cur].get(0).vertex;
            int edgeCost = path[cur].get(0).cost;
            
            maxEdge = Math.max(maxEdge, edgeCost);
            cur = next;
        }
        
        System.out.println(dist[V2] - maxEdge);
    }    //    main-end
    
    private static boolean dfs(int v, int cost) {
        visited[v] = true;
        dist[v] = cost;
        
        if(v == V2)
        	return true;
        
        boolean find = false;
        for(Edge edge : tree[v]) {
            int nv = edge.vertex;
            int ncost = cost + edge.cost;
            
            if(!visited[nv]) {
                if(dfs(nv, ncost)) {
                    path[v].add(new Edge(nv, edge.cost));
                    find = true;
                    break;
                }
            }
        }
        
        return find;
    }
    
    private static class Edge {
        public int vertex;
        public int cost;
        
        public Edge(int vertex, int cost) {
            this.vertex = vertex;
            this.cost = cost;
        }
    }
}    //    Main-class-end
```

### BOJ 2295 세 수의 합

[](https://www.acmicpc.net/problem/2295)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static int N;    		//  자연수 개수
    private static int[] nums;		//	자연수 집합
    
    private static final List<Sum> sumList = new ArrayList<>();
    private static final List<Diff> diffList = new ArrayList<>();
    
    private static int maxK = Integer.MIN_VALUE;
    
    public static void main(String[] args) throws IOException {
    	N = Integer.parseInt(br.readLine());
   
    	nums = new int[N];
    	for(int i = 0; i < N; i++)
    		nums[i] = Integer.parseInt(br.readLine());
    	
    	Arrays.sort(nums);
    	
    	for(int x = 0; x < N; x++) {
    		for(int y = x; y < N; y++) {
    			sumList.add(new Sum(nums[x] + nums[y], x, y));
    			diffList.add(new Diff(nums[y] - nums[x], y, x));
    		}
    	}
    	
    	Collections.sort(sumList);
    	Collections.sort(diffList);
    	
    	for(Sum xPlusY : sumList) {
    		int x = xPlusY.x;
    		int y = xPlusY.y;
    		int sum = xPlusY.sum;	//	nums[x] + nums[y]
    		
    		maxK = Math.max(maxK, binarySearch(y, sum));
    	}
    	
    	System.out.println(nums[maxK]);
    }    //    main-end
    
    private static int binarySearch(int y, int sum) {
    	int start = 0;
    	int end = diffList.size() - 1;
    	
    	int ret = -1;
    	
    	while(start <= end) {
    		int mid = (start + end) / 2;
    		
    		Diff kMinusZ = diffList.get(mid);
    		int diff = kMinusZ.diff;	//	nums[k] - nums[z] 값
    		int k = kMinusZ.k;
    		int z = kMinusZ.z;
    		
    		if(diff < sum) 	//	nums[k] - nums[z]이 nums[x] + nums[y]보다 작을 경우, 오른쪽에서 찾아야 함
    			start = mid + 1;
    		else if(sum < diff)		//	nums[k] - nums[z]이 nums[x] + nums[y]보다 클 경우, 오른쪽에서 찾아야 함
    			end = mid - 1;
    		else {	//	nums[k] - nums[z]와 nums[x] + nums[y]가 같을 경우
    			if(z < y)	//	z가 y보다 더 작을 경우, 더 오른쪽에서 찾아야 함
    				start = mid + 1;
    			else {	//	z가 y보다 크거나 같을 경우, 일단 k값을 저장하고 더 오른쪽에서 찾아보기
    				ret = k;
    				start = mid + 1;
    			}
    		}
    	}
    	
    	return ret;
    }
    
    private static class Sum implements Comparable<Sum> {
    	public int sum;	//	nums[x] + nums[y]
    	public int x;
    	public int y;
    	
    	public Sum(int sum, int x, int y) {
    		this.sum = sum;
    		this.x = x;
    		this.y = y;
    	}
    	
    	@Override
    	public int compareTo(Sum other) {
    		return Integer.compare(this.sum, other.sum);	//	nums[x] + nums[y] 기준으로 오름차순 정렬
    	}
    }
    
    private static class Diff implements Comparable<Diff> {
    	public int diff;	//	nums[k] - nums[z]
    	public int k;
    	public int z;
    	
    	public Diff(int diff, int k, int z) {
    		this.diff = diff;
    		this.k = k;
    		this.z = z;
    	}
    	
    	@Override
    	public int compareTo(Diff other) {
    		int ret = Integer.compare(this.diff, other.diff);	//	nums[k] - nums[z] 기준으로 오름차순 정렬
    		
    		if(ret == 0)
    			ret = Integer.compare(this.z, other.z);	//	nums[k] - nums[z]가 같을 경우 z에 대해 오름차순 정렬
    		
    		return ret;
    	}
    }
}    //    Main-class-end
```