# 25_02_08_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16971 배열 B의 값

[16971번: 배열 B의 값](http://boj.ma/16971/t)

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
    
    private static int[][] orig;
    private static int[][] tmp;
    
    private static long answer = Integer.MIN_VALUE;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        orig = new int[N + 1][M + 1];
        tmp = new int[N + 1][M + 1];
        
        for(int y = 1; y <= N; y++) {
        	st = new StringTokenizer(br.readLine());
        	for(int x = 1; x <= M; x++) {
        		orig[y][x] = Integer.parseInt(st.nextToken());
        		tmp[y][x] = orig[y][x];
        	}
        }
        
        answer = bValue();	//	아무것도 바꾸지 않았을때
        answer = Math.max(answer, swapCol());	//	열 교환했을때
        copy();
        answer = Math.max(answer, swapRow());	//	행 교환했을때
        
        System.out.println(answer);
    }    //    main-end
    
    private static void copy() {
    	for(int y = 1; y <= N; y++) {
        	for(int x = 1; x <= M; x++)
        		tmp[y][x] = orig[y][x];
        }
    }
    
    private static long swapCol() {
    	long maxColSum = Long.MIN_VALUE;
    	int maxCol = 0;
    	long minColSum = Long.MAX_VALUE;
    	int minCol = 0;
    	
    	for(int col = 1; col <= M; col += M - 1) {
    		long colSum = tmp[1][col] + tmp[N][col];
    		
    		for(int row = 2; row < N; row++)
    			colSum += 2 * tmp[row][col];
    		
    		if(colSum > maxColSum) {
    			maxColSum = colSum;
    			maxCol = col;
    		}
    	}
    	
    	for(int col = 2; col < M; col++) {
    		long colSum = 2 * tmp[1][col] + 2 * tmp[N][col];
    		
    		for(int row = 2; row < N; row++)
    			colSum += 4 * tmp[row][col];
    		
    		if(colSum < minColSum) {
    			minColSum = colSum;
    			minCol = col;
    		}
    	}
    	
    	for(int row = 1; row <= N; row++) {
    		int temp = tmp[row][minCol];
    		tmp[row][minCol] = tmp[row][maxCol];
    		tmp[row][maxCol] = temp;
    	}
    	
    	return bValue();
    }
    
    private static long swapRow() {
    	long maxRowSum = Long.MIN_VALUE;
    	int maxRow = 0;
    	long minRowSum = Long.MAX_VALUE;
    	int minRow = 0;
    	
    	for(int row = 1; row <= N; row += N - 1) {
    		long rowSum = tmp[row][1] + tmp[row][M];
    		
    		for(int col = 2; col < M; col++)
    			rowSum += 2 * tmp[row][col];
    		
    		if(rowSum > maxRowSum) {
    			maxRowSum = rowSum;
    			maxRow = row;
    		}
    	}
    	
    	for(int row = 2; row < N; row++) {
    		long rowSum = 2 * tmp[row][1] + 2 * tmp[row][M];
    		
    		for(int col = 2; col < M; col++)
    			rowSum += 4 * tmp[row][col];
    		
    		if(rowSum < minRowSum) {
    			minRowSum = rowSum;
    			minRow = row;
    		}
    	}
    	
    	for(int col = 1; col <= M; col++) {
    		int temp = tmp[minRow][col];
    		tmp[minRow][col] = tmp[maxRow][col];
    		tmp[maxRow][col] = temp;
    	}
    	
    	return bValue();
    }
    
    private static long bValue() {
    	long res = 0;
    	
    	for(int y = 1; y <= N; y++) {
    		for(int x = 1; x <= M; x++) {
    			if((y == 1 && x == 1) || (y == 1 && x == M) || (y == N && x == 1) || (y == N && x == M))
    				res += tmp[y][x];
    			else if(y == 1 || y == N || x == 1 || x == M)
    				res += 2 * tmp[y][x];
    			else
    				res += 4 * tmp[y][x];
    		}
    	}
    	
    	return res;
    }
}    //    Main-class-end
```