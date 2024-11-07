# 24_11_07_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 5095 Matrix Powers

[](https://www.acmicpc.net/problem/5095)

```java
import java.io.*;
import java.util.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    private static int P;
    
    private static int[][] mat;
    
    public static void main(String[] args) throws IOException {
        while(true) {
	    	st = new StringTokenizer(br.readLine());
	        N = Integer.parseInt(st.nextToken());
	        M = Integer.parseInt(st.nextToken());
	        P = Integer.parseInt(st.nextToken());
        
        	if(N == 0 && M == 0 && P == 0) {
        		System.out.print(sb);
        		break;
        	}
        	
        	long[][] mat = new long[N][N];
        	for(int r = 0; r < N; r++) {
        		st = new StringTokenizer(br.readLine());
        		for(int c = 0; c < N; c++)
        			mat[r][c] = Integer.parseInt(st.nextToken());
        	}
        	
        	print(pow(mat, P));
        }
    }	//	main-end
    
    private static void print(long[][] mat) {
    	for(int r = 0; r < N; r++) {
    		for(int c = 0; c < N; c++)
    			sb.append(mat[r][c]).append(" ");
    		sb.append("\n");
    	}
    	
    	sb.append("\n");
    }
    
    private static long[][] mult(long[][] mat1, long[][] mat2) {
    	long[][] res = new long[N][N];
    	
    	for(int i = 0; i < N; i++) {
    		for(int j = 0; j < N; j++) {
    			for(int k = 0; k < N; k++) {
    				res[i][j] += (mat1[i][k] % M * mat2[k][j] % M);
    				res[i][j] %= M;
    			}
    		}
    	}
    	
    	return res;
    }
    
    private static long[][] pow(long[][] mat, int exp) {
    	if(exp == 1)
    		return mat;
    	
    	long[][] res = pow(mat, exp >> 1);
    	res = mult(res, res);
    	
    	if((exp & 1) == 1)
    		res = mult(res, mat);
    	
    	return res;
    }
}	//	Main-class-end
```