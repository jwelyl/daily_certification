# 24_06_02_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 겹쳐지지 않는 두 직사각형**

[https://www.codetree.ai/missions/2/problems/non-overlapping-two-rectangles/description](https://www.codetree.ai/missions/2/problems/non-overlapping-two-rectangles/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/2/problems/non-overlapping-two-rectangles/description)

**코드** 

```java
import java.util.*;
import java.io.*;

public class Main {
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static StringTokenizer tokens;
  private static int n, m;
  private static int[][] prefixSum;
  
  private static int maxSum = Integer.MIN_VALUE;

  public static void main(String[] args) throws IOException {
    tokens = new StringTokenizer(br.readLine());
    n = Integer.parseInt(tokens.nextToken());
    m = Integer.parseInt(tokens.nextToken());

    prefixSum = new int[n + 1][m + 1];
    
    for(int i = 1; i <= n; i++) {
      tokens = new StringTokenizer(br.readLine());
      for(int j = 1; j <= m; j++) 
        prefixSum[i][j] = prefixSum[i - 1][j] + prefixSum[i][j - 1] - prefixSum[i - 1][j - 1] + Integer.parseInt(tokens.nextToken());
    }
    
    for(int y11 = 1; y11 <= n; y11++) {
    	for(int x11 = 1; x11 <= m; x11++) {
    		for(int y12 = y11; y12 <= n; y12++) {
    			for(int x12 = x11; x12 <= m; x12++) {
    				
    				for(int y21 = 1; y21 <= n; y21++) {
    			    	for(int x21 = 1; x21 <= m; x21++) {
    			    		for(int y22 = y21; y22 <= n; y22++) {
    			    			for(int x22 = x21; x22 <= m; x22++) {
    			    				if(nonOverlapped(y11, x11, y12, x12, y21, x21, y22, x22))	//	두 직사각형이 겹치지 않을 경
    			    					maxSum = Math.max(maxSum, partialSum(y11, x11, y12, x12) + partialSum(y21, x21, y22, x22));
    			    			}
    			    		}
    			    	}
    			    }
    				
    				
    			}
    		}
    	}
    }
    
    System.out.println(maxSum);
  } //  main-end
  
  private static boolean nonOverlapped(int y11, int x11, int y12, int x12, int y21, int x21, int y22, int x22) {
	  boolean[][] visited = new boolean[n + 1][m + 1];
	  
	  for(int i = y11; i <= y12; i++) {
		  for(int j = x11; j <= x12; j++)
			  visited[i][j] = true;
	  }
	  
	  for(int i = y21; i <= y22; i++) {
		  for(int j = x21; j <= x22; j++) {
			  if(visited[i][j])
				  return false;
		  }
	  }
	  
	  return true;
  }
  
  private static int partialSum(int y1, int x1, int y2, int x2) {
	  return prefixSum[y2][x2] - prefixSum[y1 - 1][x2] - prefixSum[y2][x1 - 1] + prefixSum[y1 - 1][x1 - 1];
  }
} //  Main-class-end
```