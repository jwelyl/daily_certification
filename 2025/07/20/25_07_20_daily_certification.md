# 25_07_20_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1443 망가진 계산기

[1443번: 망가진 계산기](http://boj.ma/1443/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashSet;
import java.util.Set;
import java.util.StringTokenizer;
 
public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int D;		//	자리수 제한
	private static int P;		//	연산 수
	
	private static long limit;	//	D자리수 자연수 중 최대 자연수

	//	visited[nth] : 곱셈 횟수가 nth일때 나온 수의 목록
	private static Set<Long>[] visited;
	
	private static long answer = -1;
	
    public static void main(String[] args) throws IOException {
    	st = new StringTokenizer(br.readLine());
    	D = Integer.parseInt(st.nextToken());
    	P = Integer.parseInt(st.nextToken());
    	
    	visited = new HashSet[P + 1];
    	for(int p = 0; p <= P; p++)
    		visited[p] = new HashSet<>();
    	
    	limit = (long)(Math.pow(10, D) - 1);
    	
    	backtracking(0, 1);
    	
    	System.out.println(answer);
    }	//	main-end
    
    private static void backtracking(int nth, long mult) {
    	if(visited[nth].contains(mult))
    		return;
    	
    	visited[nth].add(mult);
    	
    	if(nth == P) {
    		answer = Math.max(answer, mult);
    		return;
    	}
    	
    	for(int digit = 2; digit <= 9; digit++) {
    		if(mult * digit > limit)
    			break;
    		
    		backtracking(nth + 1, mult * digit);
    	}
    }
}	//	Main-class-end
```