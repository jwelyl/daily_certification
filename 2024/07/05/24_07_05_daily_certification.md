# 24_07_05_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 3019 테트리스

[](https://www.acmicpc.net/problem/3019)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer tokens;
	
	private static int C;
	private static int P;
	
	private static int[] heights;
	
	private static int cnt = 0;
	
	public static void main(String[] args) throws IOException {
		tokens = new StringTokenizer(br.readLine());
		C = Integer.parseInt(tokens.nextToken());
		P = Integer.parseInt(tokens.nextToken());
		heights = new int[C];
		
		tokens = new StringTokenizer(br.readLine());
		for(int c = 0; c < C; c++)
			heights[c] = Integer.parseInt(tokens.nextToken());
		
		switch(P) {
		case 1:
			cnt += C;
			
			for(int c = 0; c <= C - 4; c++) {
				int init = heights[c];
				boolean ok = true;
				
				for(int i = 1; i < 4; i++) {
					if(init != heights[c + i]) {
						ok = false;
						break;
					}
				}
				
				if(ok)
					cnt++;
			}
			
			break;
		case 2:
			for(int c = 0; c <= C - 2; c++) {
				if(heights[c] == heights[c + 1])
					cnt++;
			}
			
			break;
		case 3:
			for(int c = 0; c <= C - 3; c++) {
				if(heights[c] == heights[c + 1] && heights[c + 1] + 1 == heights[c + 2])
					cnt++;
			}
			
			for(int c = 0; c <= C - 2; c++) {
				if(heights[c] == heights[c + 1] + 1)
					cnt++;
			}
			break;
		case 4:
			for(int c = 0; c <= C - 3; c++) {
				if(heights[c] == heights[c + 1] + 1 && heights[c + 1] == heights[c + 2])
					cnt++;
			}
			
			for(int c = 0; c <= C - 2; c++) {
				if(heights[c] + 1 == heights[c + 1])
					cnt++;
			}
			break;
		case 5:
			for(int c = 0; c <= C - 3; c++) {
				int init = heights[c];
				boolean ok = true;
				
				for(int i = 1; i < 3; i++) {
					if(init != heights[c + i]) {
						ok = false;
						break;
					}
				}
				
				if(ok)
					cnt++;
			}
			
			for(int c = 0; c <= C - 2; c++) {
				if(heights[c] + 1 == heights[c + 1])
					cnt++;
			}
			
			for(int c = 0; c <= C - 3; c++) {
				if(heights[c] == heights[c + 2] && heights[c] == heights[c + 1] + 1)
					cnt++;
			}
			
			for(int c = 0; c <= C - 2; c++) {
				if(heights[c] == heights[c + 1] + 1)
					cnt++;
			}
			
			break;
		case 6:
			for(int c = 0; c <= C - 3; c++) {
				int init = heights[c];
				boolean ok = true;
				
				for(int i = 1; i < 3; i++) {
					if(init != heights[c + i]) {
						ok = false;
						break;
					}
				}
				
				if(ok)
					cnt++;
			}
			
			for(int c = 0; c <= C - 2; c++) {
				if(heights[c] == heights[c + 1])
					cnt++;
			}
			
			for(int c = 0; c <= C - 3; c++) {
				if(heights[c + 1] == heights[c + 2] && heights[c] == heights[c + 1] - 1)
					cnt++;
			}
			
			for(int c = 0; c <= C - 2; c++) {
				if(heights[c] == heights[c + 1] + 2)
					cnt++;
			}
			
			break;
		case 7:
			for(int c = 0; c <= C - 3; c++) {
				int init = heights[c];
				boolean ok = true;
				
				for(int i = 1; i < 3; i++) {
					if(init != heights[c + i]) {
						ok = false;
						break;
					}
				}
				
				if(ok)
					cnt++;
			}
			
			for(int c = 0; c <= C - 2; c++) {
				if(heights[c] + 2 == heights[c + 1])
					cnt++;
			}
			
			for(int c = 0; c <= C - 3; c++) {
				if(heights[c] == heights[c + 1] && heights[c + 2] == heights[c + 1] - 1)
					cnt++;
			}
			
			for(int c = 0; c <= C - 2; c++) {
				if(heights[c] == heights[c + 1])
					cnt++;
			}
			
			break;
		}
		
		System.out.println(cnt);
	}	//	main-end
}	//	Main-class-end
```