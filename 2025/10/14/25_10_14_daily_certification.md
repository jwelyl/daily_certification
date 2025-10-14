# 25_10_14_daily_certification

# To Do List

```
[#287 koreii] 데일리인증 20251014
1. 알고리즘 문제 풀이
- BOJ 24492 Cow Frisbee (Stack)
2. 예비군
```

```
[#106] 데일리인증 20251014 (287 / 365)
1. 서류 작성
2. 알고리즘 문제 풀이
- BOJ 22858 원상 복구 (Small) (Simulation)
- BOJ 22863 원상 복구 (Large) (Sparse Table)
```

# Problem Solving (Algorithm & SQL)

### BOJ 22863 원상 복구 (Large)

[22863번: 원상 복구 (large)](http://boj.ma/22863/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final int MAX = (int)Math.ceil(Math.log(Math.pow(10, 15)) / Math.log(2));
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;
    private static long K;
    
    private static int[] P;
    private static int[][] sparseTable;
    
    private static int[] answer;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        K = Long.parseLong(st.nextToken());
        
        P = new int[N + 1];
        sparseTable = new int[MAX + 1][N + 1];
        answer = new int[N + 1];
        
        st = new StringTokenizer(br.readLine());
        for(int i = 1; i <= N; i++)
            P[i] = Integer.parseInt(st.nextToken());
        st = new StringTokenizer(br.readLine());
        for(int i = 1; i <= N; i++)
            sparseTable[0][i] = Integer.parseInt(st.nextToken());
        
        makeSparseTable();
        getAnswer();
        
        for(int i = 1; i <= N; i++)
            sb.append(answer[i]).append(" ");
        System.out.println(sb);
    }    //    main-end
    
    private static void getAnswer() {
        for(int i = 1; i <= N; i++) {
        	int idx = i;
        	
	    	for(int d = MAX; d >= 0; d--) {
	        	if((K & (1L << d)) != 0)
	        		idx = sparseTable[d][idx];
	        }
	    	
	    	answer[idx] = P[i];
        }
    }
    
    private static void makeSparseTable() {
        for (int d = 1; d <= MAX; d++) {
            for (int i = 1; i <= N; i++)
                sparseTable[d][i] = sparseTable[d - 1][sparseTable[d - 1][i]];
        }
    }
}    //    Main-class-end
```