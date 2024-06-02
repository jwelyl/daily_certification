# 24_06_03_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 양수 직사각형의 최대 크기**

[https://www.codetree.ai/missions/2/problems/max-area-of-positive-rectangle/description](https://www.codetree.ai/missions/2/problems/max-area-of-positive-rectangle/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/2/problems/max-area-of-positive-rectangle/description)

**코드** 

```java
import java.util.*;
import java.io.*;

public class Main {
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static StringTokenizer tokens;
  private static int n, m;
  private static int[][] nums;
  
  private static int maxSize = -1;

  public static void main(String[] args) throws IOException {
    tokens = new StringTokenizer(br.readLine());
    n = Integer.parseInt(tokens.nextToken());
    m = Integer.parseInt(tokens.nextToken());

    nums = new int[n][m];
    
    for(int i = 0; i < n; i++) {
      tokens = new StringTokenizer(br.readLine());
      for(int j = 0; j < m; j++) 
        nums[i][j] = Integer.parseInt(tokens.nextToken());
    }
    
    for(int y1 = 0; y1 < n; y1++) {
    	for(int x1 = 0; x1 < m; x1++) {
    		for(int y2 = y1; y2 < n; y2++) {
    			for(int x2 = x1; x2 < m; x2++) {
    				if(isPositive(y1, x1, y2, x2))
    					maxSize = Math.max(maxSize, (y2 - y1 + 1) * (x2 - x1 + 1));
    			}
    		}
    	}
    }
    
    
    System.out.println(maxSize);
  } //  main-end
  
  private static boolean isPositive(int y1, int x1, int y2, int x2) {
	  for(int i = y1; i <= y2; i++) {
		  for(int j = x1; j <= x2; j++) {
			  if(nums[i][j] <= 0)
				  return false;
		  }
	  }
	  
	  return true;
  }
} //  Main-class-end
```