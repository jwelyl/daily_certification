# 25_10_21_daily_certification

# To Do List

# Problem Solving (Algorithm & SQL)

### BOJ 14628 입 챌린저

[14628번: 입 챌린저](http://boj.ma/14628/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Arrays;

public class Main {
    private static final int MAX = 1_000 * 1_000 * 1_000;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    스킬 개수
    private static int M;    //    적의 체력
    private static int K;    //    스킬을 한 번 사용할때마다 추가되는 마나 비용
    
    private static int[][] skills;    //    skills[i] : i번째 스킬의 {기본 마나 코스트, 데미지}
    
    //    dp[i][j] : 1 ~ j번 스킬 중에서 사용해서 적에게 정확히 j의 데미지를 주기 위해 필요한 최소 마나
    private static int[][] dp;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        skills = new int[N + 1][2];
        dp = new int[N + 1][M + 1];
        for(int i = 0; i <= N; i++) {
            Arrays.fill(dp[i], MAX);
            dp[i][0] = 0;
        }
        
        for(int i = 1; i <= N; i++) {
            st = new StringTokenizer(br.readLine());
            skills[i][0] = Integer.parseInt(st.nextToken());
            skills[i][1] = Integer.parseInt(st.nextToken());
        }
        
        for(int i = 1; i <= N; i++) {
            int[] skill = skills[i];
            
            for(int j = 1; j <= M; j++) {
                for(int k = 0; ; k++) {
                    int cost = k == 0 ? 0 : k * skill[0] + k * (k - 1) * K / 2;    //    i번째 스킬을 k번 사용했을 때 필요한 누적 마나
                    int damage = k * skill[1];                                     //    i번째 스킬을 k번 사용했을 때 줄 수 있는 데미지
                
                    if(damage > j)
                        break;
                    
                    dp[i][j] = Math.min(dp[i][j], dp[i - 1][j - damage] + cost);
                }
            }
        }
        
        System.out.println(dp[N][M]);    
    }    //    main-end
}    //    Main-class-end
```

### BOJ 1976 여행 가자

[1976번: 여행 가자](http://boj.ma/1976/t)

**Stack DFS로 매번 이동 가능 체크**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;

    private static int N;
    private static int M;
    
    private static List<Integer>[] graph;
    private static int src;
    private static int dst;

    public static void main(String[] args) throws IOException {
    	N = Integer.parseInt(br.readLine());
    	M = Integer.parseInt(br.readLine());
    	
    	graph = new ArrayList[N + 1];
    	for(int v = 1; v <= N; v++)
    		graph[v] = new ArrayList<>();
    	
    	for(int v1 = 1; v1 <= N; v1++) {
    		st = new StringTokenizer(br.readLine());
    		for(int v2 = 1; v2 <= N; v2++) {
    			int conn = Integer.parseInt(st.nextToken());
    			
    			if(conn == 1)
    				graph[v1].add(v2);
    		}
    	}
    	
    	st = new StringTokenizer(br.readLine());
    	src = Integer.parseInt(st.nextToken());
    	for(int i = 1; i < M; i++) {
    		dst = Integer.parseInt(st.nextToken());
    		if(!dfs(src, dst)) {
    			System.out.println("NO");
    			return;
    		}
    		src = dst;
    	}
    	
    	System.out.println("YES");
    } // main-end
    
    //	src에서 dst에 도달 가능하면 true
    private static boolean dfs(int src, int dst) {
        if(src == dst)
            return true;
        
    	Deque<Integer> stack = new ArrayDeque<>();
    	boolean[] visited = new boolean[N + 1];
    	visited[src] = true;
    	stack.offerLast(src);
    	
    	while(!stack.isEmpty()) {
    		int cur = stack.pollLast();
    		
    		for(int next : graph[cur]) {
    			if(next == dst)
    				return true;
    			
    			if(!visited[next]) {
    				visited[next] = true;
    				stack.offerLast(next);
    			}
    		}
    	}
    	
    	return false;
    }
} // Main-class-end
```

**Stack DFS + CC 전처리**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;

    private static int N;
    private static int M;
    
    private static List<Integer>[] graph;
    private static int color = 1;
    private static int[] colors;
    private static int src;
    private static int dst;

    public static void main(String[] args) throws IOException {
    	N = Integer.parseInt(br.readLine());
    	M = Integer.parseInt(br.readLine());
    	
    	graph = new ArrayList[N + 1];
    	colors = new int[N + 1];
    	for(int v = 1; v <= N; v++)
    		graph[v] = new ArrayList<>();
    	
    	for(int v1 = 1; v1 <= N; v1++) {
    		st = new StringTokenizer(br.readLine());
    		for(int v2 = 1; v2 <= N; v2++) {
    			int conn = Integer.parseInt(st.nextToken());
    			
    			if(conn == 1)
    				graph[v1].add(v2);
    		}
    	}
    	
    	for(int v = 1; v <= N; v++) {
    		if(colors[v] == 0)
    			dfs(v, color++);
    	}
    	
    	st = new StringTokenizer(br.readLine());
    	src = Integer.parseInt(st.nextToken());
    	for(int i = 1; i < M; i++) {
    		dst = Integer.parseInt(st.nextToken());
    		if(colors[src] != colors[dst]) {
    			System.out.println("NO");
    			return;
    		}
    		
    		src = dst;
    	}
    	
    	System.out.println("YES");
    } // main-end
    
    private static void dfs(int v, int color) {
    	Deque<Integer> stack = new ArrayDeque<>();
    	colors[v] = color;
    	stack.offerLast(v);
    	
    	while(!stack.isEmpty()) {
    		int cur = stack.pollLast();
    		
    		for(int next : graph[cur]) {
    			if(colors[next] == 0) {
    				colors[next] = color;
    				stack.offerLast(next);
    			}
    		}
    	}
    }
} // Main-class-end
```

**BFS로 매번 이동 가능 체크**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;

    private static int N;
    private static int M;
    
    private static List<Integer>[] graph;
    private static int src;
    private static int dst;

    public static void main(String[] args) throws IOException {
    	N = Integer.parseInt(br.readLine());
    	M = Integer.parseInt(br.readLine());
    	
    	graph = new ArrayList[N + 1];
    	for(int v = 1; v <= N; v++)
    		graph[v] = new ArrayList<>();
    	
    	for(int v1 = 1; v1 <= N; v1++) {
    		st = new StringTokenizer(br.readLine());
    		for(int v2 = 1; v2 <= N; v2++) {
    			int conn = Integer.parseInt(st.nextToken());
    			
    			if(conn == 1)
    				graph[v1].add(v2);
    		}
    	}
    	
    	st = new StringTokenizer(br.readLine());
    	src = Integer.parseInt(st.nextToken());
    	for(int i = 1; i < M; i++) {
    		dst = Integer.parseInt(st.nextToken());
    		if(!bfs(src, dst)) {
    			System.out.println("NO");
    			return;
    		}
    		src = dst;
    	}
    	
    	System.out.println("YES");
    } // main-end
    
//	src에서 dst에 도달 가능하면 true
    private static boolean bfs(int src, int dst) {
        if(src == dst)
            return true;
        
    	Deque<Integer> queue = new ArrayDeque<>();
    	boolean[] visited = new boolean[N + 1];
    	visited[src] = true;
    	queue.offerLast(src);
    	
    	while(!queue.isEmpty()) {
    		int cur = queue.pollFirst();
    		
    		for(int next : graph[cur]) {
    			if(next == dst)
    				return true;
    			
    			if(!visited[next]) {
    				visited[next] = true;
    				queue.offerLast(next);
    			}
    		}
    	}
    	
    	return false;
    }
} // Main-class-end
```

**BFS + CC 전처리**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.List;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;

    private static int N;
    private static int M;
    
    private static List<Integer>[] graph;
    private static int color = 1;
    private static int[] colors;
    private static int src;
    private static int dst;

    public static void main(String[] args) throws IOException {
    	N = Integer.parseInt(br.readLine());
    	M = Integer.parseInt(br.readLine());
    	
    	graph = new ArrayList[N + 1];
    	colors = new int[N + 1];
    	for(int v = 1; v <= N; v++)
    		graph[v] = new ArrayList<>();
    	
    	for(int v1 = 1; v1 <= N; v1++) {
    		st = new StringTokenizer(br.readLine());
    		for(int v2 = 1; v2 <= N; v2++) {
    			int conn = Integer.parseInt(st.nextToken());
    			
    			if(conn == 1)
    				graph[v1].add(v2);
    		}
    	}
    	
    	for(int v = 1; v <= N; v++) {
    		if(colors[v] == 0)
    			bfs(v, color++);
    	}
    	
    	st = new StringTokenizer(br.readLine());
    	src = Integer.parseInt(st.nextToken());
    	for(int i = 1; i < M; i++) {
    		dst = Integer.parseInt(st.nextToken());
    		if(colors[src] != colors[dst]) {
    			System.out.println("NO");
    			return;
    		}
    		
    		src = dst;
    	}
    	
    	System.out.println("YES");
    } // main-end
    
    private static void bfs(int v, int color) {
    	Queue<Integer> queue = new ArrayDeque<>();
    	colors[v] = color;
    	queue.offer(v);
    	
    	while(!queue.isEmpty()) {
    		int cur = queue.poll();
    		
    		for(int next : graph[cur]) {
    			if(colors[next] == 0) {
    				colors[next] = color;
    				queue.offer(next);
    			}
    		}
    	}
    }
} // Main-class-end
```

**Disjoint Set 전처리**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;

    private static int N;
    private static int M;
    
    private static int[] parents;
    private static int src;
    private static int dst;

    public static void main(String[] args) throws IOException {
    	N = Integer.parseInt(br.readLine());
    	M = Integer.parseInt(br.readLine());
    	
    	parents = new int[N + 1];
    	for(int v = 1; v <= N; v++)
    		parents[v] = v;
    		
    	for(int v1 = 1; v1 <= N; v1++) {
    		st = new StringTokenizer(br.readLine());
    		for(int v2 = 1; v2 <= N; v2++) {
    			int conn = Integer.parseInt(st.nextToken());
    			
    			if(conn == 1)
    				union(v1, v2);
    		}
    	}
    	
    	st = new StringTokenizer(br.readLine());
    	src = Integer.parseInt(st.nextToken());
    	for(int i = 1; i < M; i++) {
    		dst = Integer.parseInt(st.nextToken());
    		if(find(src) != find(dst)) {
    			System.out.println("NO");
    			return;
    		}
    		
    		src = dst;
    	}
    	
    	System.out.println("YES");
    } // main-end
    
    private static int find(int v) {
    	return v == parents[v] ? v : (parents[v] = find(parents[v]));
    }
    
    private static void union(int v1, int v2) {
    	v1 = find(v1);
    	v2 = find(v2);
    	
    	if(v1 != v2)
    		parents[v2] = v1;
    }
} // Main-class-end
```

**Floyd Warshall 전처리**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int INF = 200 * 200 * 200;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;

    private static int N;
    private static int M;
    
    private static int[][] dp;
    private static int src;
    private static int dst;

    public static void main(String[] args) throws IOException {
    	N = Integer.parseInt(br.readLine());
    	M = Integer.parseInt(br.readLine());
    	
    	dp = new int[N + 1][N + 1];
    	for(int v1 = 1; v1 <= N; v1++) {
    		for(int v2 = 1; v2 <= N; v2++)
    			dp[v1][v2] = INF;
    		
    		dp[v1][v1] = 0;
    	}
    		
    	for(int v1 = 1; v1 <= N; v1++) {
    		st = new StringTokenizer(br.readLine());
    		for(int v2 = 1; v2 <= N; v2++) {
    			int conn = Integer.parseInt(st.nextToken());
    			
    			if(conn == 1) {
    				dp[v1][v2] = 1;
    				dp[v2][v1] = 1;
    			}
    		}
    	}
    	
    	floydWarshall();
    	
    	st = new StringTokenizer(br.readLine());
    	src = Integer.parseInt(st.nextToken());
    	for(int i = 1; i < M; i++) {
    		dst = Integer.parseInt(st.nextToken());
    		if(dp[src][dst] == INF) {
    			System.out.println("NO");
    			return;
    		}
    		
    		src = dst;
    	}
    	
    	System.out.println("YES");
    } // main-end
    
    private static void floydWarshall() {
    	for(int k = 1; k <= N; k++) {
    		for(int i = 1; i <= N; i++) {
    			for(int j = 1; j <= N; j++)
    				dp[i][j] = Math.min(dp[i][j], dp[i][k] + dp[k][j]);
    		}
    	}
    }
} // Main-class-end
```

### 프로그래머스 최고의 집합

[](https://school.programmers.co.kr/learn/courses/30/lessons/12938)

```java
class Solution {
    public int[] solution(int n, int s) {
        int[] answer = {-1};
        
        if(s >= n) {
            answer = new int[n];
            if(s % n == 0) {
                for(int i = 0; i < n; i++)
                    answer[i] = s / n;
            }
            else {
                int q = s / n;
                int r = s % n;
                for(int i = 0; i < n; i++)
                    answer[i] = q;
                
                for(int i = n - 1; i >= n - r; i--)
                    answer[i]++;
            }
        }
        
        return answer;
    }
}
```