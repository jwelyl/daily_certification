# 25_08_28_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 9997 폰트

[9997번: 폰트](http://boj.ma/9997/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Map;
import java.util.HashMap;

public class Main {
	private static final int FULL = (1 << 26) - 1;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static int N;
    
    private static int[] binaries;
    
    private static int total = 0;
    private static int answer = 0;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        binaries = new int[N];
        for(int i = 0; i < N; i++) {
        	int bin = wordToBinary(br.readLine());
        	binaries[i] = bin;
        	total |= bin;
        }
        
        if(total == FULL)	//	N개의 단어들에 'a' ~ 'z'가 적어도 하나씩 포함된 경우
        	backtracking(0, 0);
        
        System.out.println(answer);
    }    //    main-end
    
    private static void backtracking(int nth, int mask) {
    	if(mask == FULL) {	//	이미 현재 상태가 26개 문자를 모두 포함할 경우
    		answer += Math.pow(2, N - nth);
    		return;
    	}
    	
    	if(nth == N)
    		return;
    	
    	//	nth번째 단어 포함 o
    	backtracking(nth + 1, mask | binaries[nth]);
    	//	nth번째 단어 포함 x
    	backtracking(nth + 1, mask);
    }
    
    private static int wordToBinary(String word) {
    	int mask = 0;
    	
    	for(int i = 0; i < word.length(); i++) {
    		char ch = word.charAt(i);
    		mask |= (1 << (ch - 'a'));
    	}
    	
    	return mask;
    }
}    //    Main-class-end
```