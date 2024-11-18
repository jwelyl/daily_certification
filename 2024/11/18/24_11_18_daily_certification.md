# 24_11_18_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 11997** Load Balancing (Silver)

[11997번: Load Balancing (Silver)](https://boj.ma/11997/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;
import java.util.Set;
import java.util.StringTokenizer;
import java.util.TreeSet;

public class Main {
   private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
   private static StringTokenizer st; 
   
   private static int N;	//	소 마리 수
   
   private static final Set<Integer> xSet = new TreeSet<>();
   private static final Set<Integer> ySet = new TreeSet<>();
   private static final Map<Integer, Integer> xMap = new HashMap<>();
   private static final Map<Integer, Integer> yMap = new HashMap<>();
   
   private static int[][] pos;			//	원본 좌표
   
   private static int[][] compressed;	//	압축된 좌표
   private static int rows = 0;
   private static int cols = 0;			//	좌표 압축했을때 행, 열 수
   
   private static int[][] prefixSum;	//	누적합
   
   private static int answer = Integer.MAX_VALUE;
   
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
       
        pos = new int[N][2];
        
        for(int cow = 0; cow < N; cow++) {
        	st = new StringTokenizer(br.readLine());
        	int x = Integer.parseInt(st.nextToken());
        	int y = Integer.parseInt(st.nextToken());
        	
        	pos[cow][0] = x;
        	pos[cow][1] = y;
        	xSet.add(x);
        	ySet.add(y);
        }
        
        int idx = 0;
        for(int x : xSet)
        	xMap.put(x, idx++);	//	x 좌표에 상대적인 위치 idx 부여
        
        rows = xMap.size();
        
        idx = 0;
        for(int y : ySet)
        	yMap.put(y, idx++);	//	y 좌표에 상대적인 위치 idx 부여
        
        cols = yMap.size();
        
        compressed = new int[rows][cols];
        prefixSum = new int[rows + 1][cols + 1]; 
        for(int cow = 0; cow < N; cow++) {
        	int x = pos[cow][0];
        	int y = pos[cow][1];	//	원래 소의 x, y 좌표
        	
        	int compressedX = xMap.get(x);	//	압축된 x 좌표
        	int compressedY = yMap.get(y);	//	압축된 y 좌표
        	
        	compressed[compressedX][compressedY]++;
        }
        
        //	누적합 구하기
        for(int row = 1; row <= rows; row++) {
        	for(int col = 1; col <= cols; col++)
        		prefixSum[row][col] = prefixSum[row - 1][col] + prefixSum[row][col - 1] - prefixSum[row - 1][col - 1] + compressed[row - 1][col - 1];
        }
        
        for(int row = 0; row <= rows; row++) {
        	for(int col = 0; col <= cols; col++) {
        		int[] quadrants = new int[4];
        		quadrants[0] = partialSum(0, 0, row, col);
        		quadrants[1] = partialSum(row, 0, rows, col);
        		quadrants[2] = partialSum(0, col, row, cols);
        		quadrants[3] = partialSum(row, col, rows, cols);
        		
        		int max = -1;
        		for(int i = 0; i < 4; i++)
        			max = Math.max(max, quadrants[i]);
        		
        		answer = Math.min(answer, max);
        	}
        }
        
        System.out.println(answer);
    } 	//	main-end
    
    //	좌상단 (x1 + 1, y1 + 1), 우하단 (x2, y2) 영역 부분합
    private static int partialSum(int x1, int y1, int x2, int y2) {
    	return prefixSum[x2][y2] - prefixSum[x2][y1] - prefixSum[x1][y2] + prefixSum[x1][y1];
    }
}	//	Main-class-end
```