# 25_09_09_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1033 칵테일

[1033번: 칵테일](http://boj.ma/1033/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;
    
    private static List<Integer>[] graph;
    private static boolean[] visited;
    private static int[] answer;
    
    public static void main(String[] args) throws IOException {
    	N = Integer.parseInt(br.readLine());

    	graph = new ArrayList[N];
    	visited = new boolean[N];
    	answer = new int[N];
    	for(int v = 0; v < N; v++) {
    		graph[v] = new ArrayList<>();
    		answer[v] = 1;
    	}

    	for(int e = 0; e < N - 1; e++) {
    		st = new StringTokenizer(br.readLine());
			int a = Integer.parseInt(st.nextToken());
			int b = Integer.parseInt(st.nextToken());
			int p = Integer.parseInt(st.nextToken());
			int q = Integer.parseInt(st.nextToken());
			
			int tmp1 = answer[b] * p;
			int tmp2 = answer[a] * q;
			int gcd = gcd(tmp1, tmp2);
			
			Arrays.fill(visited, false);
    	
			dfs(a, tmp1 / gcd);
			dfs(b, tmp2 / gcd);
			graph[a].add(b);
			graph[b].add(a);
    	}
    	
    	for(int v = 0; v < N; v++)
    		sb.append(answer[v]).append(" ");
    	System.out.println(sb);
    }    //    main-end
    
    private static void dfs(int v, int mult) {
    	answer[v] *= mult;
    	visited[v] = true;
    	
    	for(int nv : graph[v]) {
    		if(!visited[nv])
    			dfs(nv, mult);
    	}
    }
    
    private static int gcd(int num1, int num2) {
    	return num2 == 0 ? num1 : gcd(num2, num1 % num2);
    }
}    //    Main-class-end
```