# 24_06_01_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 트로미노**

[https://www.codetree.ai/missions/2/problems/tromino/description](https://www.codetree.ai/missions/2/problems/tromino/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/2/problems/tromino/description)

**코드** 

```java
import java.util.*;
import java.io.*;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer tokens;

	private static final boolean[][][][] trominos = {
			{ 
			  { 
				{ true, true, false }, 
				{ true, false, false }, 
				{ false, false, false } 
			  },
			  { 
				{ true, false, false }, 
				{ true, true, false }, 
				{ false, false, false } 
			  },
			  { 
				{ false, true, false }, 
				{ true, true, false }, 
				{ false, false, false } 
			  },
			  { 
				{ true, true, false }, 
				{ false, true, false }, 
				{ false, false, false } 
			  } 
			},
			{ 
			  { 
				{ true, false, false }, 
				{ true, false, false }, 
				{ true, false, false } 
			  },
			  { 
				{ true, true, true }, 
				{ false, false, false }, 
				{ false, false, false } 
			  },
			  { 
				{ true, false, false }, 
				{ true, false, false }, 
				{ true, false, false } 
			  },
			  { 
				{ true, true, true }, 
				{ false, false, false }, 
				{ false, false, false } 
			  } 
			} 
	};

	private static int n, m;
	private static int[][] board;
	private static int maxSum = 0;

	public static void main(String[] args) throws IOException {
		tokens = new StringTokenizer(br.readLine());
		n = Integer.parseInt(tokens.nextToken());
		m = Integer.parseInt(tokens.nextToken());

		board = new int[n][m];

		for (int i = 0; i < n; i++) {
			tokens = new StringTokenizer(br.readLine());
			for (int j = 0; j < m; j++)
				board[i][j] = Integer.parseInt(tokens.nextToken());
		}
		
		for(int t = 0; t < 2; t++) {	//	블록 종류
			for(int r = 0; r < 4; r++) {	//	회전 상태
				boolean[][] tromino = trominos[t][r];
				
				for(int y = 0; y < n; y++) {
					for(int x = 0; x < m; x++) {	//	블록 위치를 (y, x)에 놓았을 때, 
						int sum = 0;	//	블록 종류가 t, 회전 상태가 r인 블록을 (y, x)에 놓았을 때 수의 합
						boolean out = false;	//	블록이 밖으로 벗어나면 true
						
						CHECK:
						for(int i = 0; i < 3; i++) {
							for(int j = 0; j < 3; j++) {
								if(tromino[i][j]) {	//	블록일 경우
									if(isIn(y + i, x + j))
										sum += board[y + i][x + j];
									else {
										out = true;	//	블록이 보드 밖으로 나감
										break CHECK;
									}
								}
							}
						}
					
						if(out)
							sum = 0;
						
						maxSum = Math.max(maxSum, sum);
					}
				}
			}
		}
		
		System.out.println(maxSum);
	}	//	main-end
	
	private static boolean isIn(int y, int x) {
		return 0 <= y && y < n && 0 <= x && x < m;
	}
}	//	Main-class-end

```