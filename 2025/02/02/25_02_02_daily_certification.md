# 25_02_02_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16947 서울 지하철 2호선

[16947번: 서울 지하철 2호선](http://boj.ma/16947/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Queue;
import java.util.LinkedList;
import java.util.Arrays;

public class Main {
    private static final int UNVISITED = Integer.MAX_VALUE;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;
    private static List<Integer>[] graph;
    private static boolean[] visited;
    private static int[] parents;
    private static int[] dists;
    private static final Queue<Integer> queue = new LinkedList<>();
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        graph = new ArrayList[N + 1];
        visited = new boolean[N + 1];
        parents = new int[N + 1];
        dists = new int[N + 1];
        for(int v = 0; v <= N; v++) {
            graph[v] = new ArrayList<>();   
            dists[v] = UNVISITED;
        }
        
        for(int e = 1; e <= N; e++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            
            graph[v1].add(v2);
            graph[v2].add(v1);
        }
        
        dfs(1, 0);
        
        bfs();
    }    //    main-end
    
    private static void bfs() {
        while(!queue.isEmpty()) {
            int cur = queue.poll();
            
            for(int next : graph[cur]) {
                if(dists[next] == UNVISITED) {
                    dists[next] = dists[cur] + 1;
                    queue.offer(next);
                }
            }
        }
        
        for(int v = 1; v <= N; v++)
            sb.append(dists[v]).append(" ");
        
        System.out.println(sb);
    }
    
    private static void dfs(int vertex, int parent) {
        visited[vertex] = true;
        parents[vertex] = parent;
        
        for(int next : graph[vertex]) {
           if(!visited[next])
               dfs(next, vertex);
            else if(parents[vertex] != next) {    //    사이클 발견
                dists[next] = 0;
                findCycle(vertex);
            }
        }
    }
    
    private static void findCycle(int vertex) {
        queue.offer(vertex);
        
        if(dists[vertex] != 0) {
            dists[vertex] = 0;
            findCycle(parents[vertex]);
        }
    }
}    //    Main-class-end
```

### **BOJ** 2731 1379와 세제곱

[2731번: 1379와 세제곱](http://boj.ma/2731/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.math.BigInteger;
import java.util.Arrays;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	
	private static int T;
	private static String S;
	
	private static char[] digits;
	private static boolean find = false;
	
	public static void main(String[] args) throws IOException {
		T = Integer.parseInt(br.readLine());
		
		for(int tc = 1; tc <= T; tc++) {
			find = false;
			S = br.readLine();
			char last = S.charAt(S.length() - 1);
			
			digits = new char[S.length()];
			Arrays.fill(digits, '0');
			
			if(last == '1' || last == '9')
				digits[digits.length - 1] = last;
			else if(last == '3')
				digits[digits.length - 1] = '7';
			else
				digits[digits.length - 1] = '3';
			
			backtracking(digits.length - 1);
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static void backtracking(int nth) {
		if(find)		//	이미 찾음
			return;

		BigInteger now = new BigInteger(new String(digits));	//	일의 자리부터 채워나가기 시작한 수
		String cubic = now.pow(3).toString();	//	그 수를 세제곱함
		
		if(cubic.endsWith(S)) {	//	세제곱수의 끝 부분이 S와 일치할 경우
			sb.append(now).append("\n");
			find = true;
			return;
		}
		
		int length = digits.length - nth;	//	채워나간 길이
		
		//	더 채워나갈 수 없거나, 채워나간 길이만큼만 비교해도 일치하지 않을 경우
		if(nth - 1 < 0 || !cubic.endsWith(S.substring(S.length() - length)))
			return;
		
		for(char digit = '0'; digit <= '9'; digit++) {
			digits[nth - 1] = digit;
			backtracking(nth - 1);
			digits[nth - 1] = '0';
		}
	}
}	//	Main-class-end
```