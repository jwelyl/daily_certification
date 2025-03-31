# 25_03_31_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16965 구간과 쿼리

[16965번: 구간과 쿼리](http://boj.ma/16965/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX = 101;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;    //	쿼리 개수
    
    private static final int[][] segments = new int[MAX][2];
    private static final List<Integer>[] graph = new ArrayList[MAX];
    
    private static int cnt = 0;	//	구간 개수
    
    public static void main(String[] args) throws IOException {
    	for(int v = 0; v < MAX; v++)
    		graph[v] = new ArrayList<>();
    	
        N = Integer.parseInt(br.readLine());
        
        for(int q = 1; q <= N; q++) {
        	st = new StringTokenizer(br.readLine());
        	int cmd = Integer.parseInt(st.nextToken());
        	
        	switch(cmd) {
        	case 1:
        		cnt++;
        		
        		int x = Integer.parseInt(st.nextToken());
        		int y = Integer.parseInt(st.nextToken());
        		segments[cnt][0] = x;
        		segments[cnt][1] = y;	//	구간 추가
        		
        		for(int v = 1; v < cnt; v++) {	//	이전 구간과 연결되는지 확인
        			int[] segment = segments[v];
        			int px = segment[0];
        			int py = segment[1];
        			
        			//	v번째 segment에서 cnt번째 segment로 갈 수 있는지 체크
        			if((x < px && px < y) || (x < py && py < y))
        				graph[v].add(cnt);
        			//  cnt번째 segment에서 v번째 segment로 갈 수 있는지 체크
        			if((px < x && x < py) || (px < y && y < py))
        				graph[cnt].add(v);
        		}
        		
        		break;
    		default:
    			int a = Integer.parseInt(st.nextToken());
        		int b = Integer.parseInt(st.nextToken());
        		
        		sb.append(bfs(a, b) ? "1\n" : "0\n");
    			break;
        	}
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static boolean bfs(int from, int to) {
    	Queue<Integer> q = new LinkedList<>();
    	boolean[] visited = new boolean[MAX];
    	
    	visited[from] = true;
    	q.offer(from);
    	
    	while(!q.isEmpty()) {
    		int cur = q.poll();
    		
    		for(int next : graph[cur]) {
    			if(!visited[next]) {
    				if(next == to)
    					return true;
    				
    				visited[next] = true;
    				q.offer(next);
    			}
    		}
    	}
    	
    	return false;
    }
}    //    Main-class-end
```

### BOJ 1759 암호 만들기

[1759번: 암호 만들기](http://boj.ma/1759/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int L;
	private static int C;
	private static char[] chs;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		L = Integer.parseInt(st.nextToken());
		C = Integer.parseInt(st.nextToken());
		
		chs = new char[C];
		st = new StringTokenizer(br.readLine());
		for(int i = 0; i < C; i++) {
			chs[i] = st.nextToken().charAt(0);
		}
		
		Arrays.sort(chs);
		combination(0, 0, 0, 0, new char[L]);
		System.out.print(sb);
	}	//	main-end
	
	private static boolean isVowel(char ch) {
		return (ch == 'a' || ch == 'e' || ch == 'i' || ch == 'o' || ch == 'u');
	}
	
	private static void combination(int nth, int startIdx, int nV, int nC, char[] result) {
		if(nth == L) {
			if(!(nV >= 1 && nC >= 2))
				return;
			
			for(char ch : result)
				sb.append(ch);
			sb.append("\n");
			return;
		}
		
		for(int i = startIdx; i < C; i++) {
			char ch = chs[i];
			result[nth] = ch;
			if(isVowel(ch)) 
				combination(nth + 1, i + 1, nV + 1, nC, result);
			else 
				combination(nth + 1, i + 1, nV, nC + 1, result);
		}
	}
}	//	Main-class-end
```