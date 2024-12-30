# 24_12_29_daily_certification

### **BOJ** 14712 **넴모넴모 (Easy)**

[14712번: 넴모넴모 (Easy)](https://boj.ma/14712/t)

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
    private static boolean[][] board;
    
    private static int answer = 0;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
    	N = Integer.parseInt(st.nextToken());
    	M = Integer.parseInt(st.nextToken());
        
    	board = new boolean[N][M];
    	
    	dfs(0);
    	System.out.println(answer);
    }	//	main-end
    
    private static void dfs(int num) {
    	if(num == N * M) {	//	끝까지 놓아서 도달한 경우
    		answer++;
    		return;
    	}
    	
    	int cy = num / M;
    	int cx = num % M;
    	boolean square = true;	//	(cy, cx)에 넴모를 놓았을때 2 * 2가 만들어질 가능성이 있으면 true
    
    	for(int d = 0; d < 3; d++) {
    		int py = cy + dy[d];
    		int px = cx + dx[d];
    		
    		if(!isIn(py, px) || !board[py][px]) {	//	주변 세 칸 중 하나라도 범위 밖이거나 넴모가 없을 경우
    			square = false;	//	(cy, cx)에 넴모를 놔도 2 * 2가 만들어지지 않음
    			break;
    		}
    	}
    	
    	if(!square) {	//	(cy, cx)에 넴모를 놓을 수 있는 경우
    		board[cy][cx] = true;	//	(cy, cx)에 넴모를 놓음
    		dfs(num + 1);			//	다음 칸으로 넘어감
    		board[cy][cx] = false;	//	원상복구
    	}
    	
    	dfs(num + 1);	//	(cy, cx)에 넴모를 놓지 않고 다음 칸으로 넘어감
    		
    }
    
    private static final int[] dy = {0, -1, -1};
    private static final int[] dx = {-1, -1, 0};
    
    private static boolean isIn(int y, int x) {
    	return (0 <= y && y < N) && (0 <= x && x < M);
    }
}    //    Main-class-end
```