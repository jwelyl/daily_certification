# 25_06_22_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16571 알파 틱택토

[16571번: 알파 틱택토](http://boj.ma/16571/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int L = -1;	//	패배
	private static final int D = 0;		//	무승부
	private static final int W = 1;		//	승리
	private static final int U = 2;		//	진행 중
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static final int[][] board = new int[3][3];
	private static int turn = 0;
	private static int player = 0;	//	승패를 예측해야 하는 플레이어
	
	public static void main(String[] args) throws IOException {
		for(int y = 0; y < 3; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < 3; x++) {
				board[y][x] = Integer.parseInt(st.nextToken());
				if(board[y][x] != 0)
					turn++;
			}
		}
	
		//	X = 1, O = 2
		player = turn % 2 + 1;
		
		int res = dfs(player);
		
		System.out.println(res == 1 ? "W" : (res == 0 ? "D" : "L"));
	} //	main-end
	
	//	현재 board 상황에서 player가 이기면 1, 비기면 0, 지면 -1 반환
	private static int dfs(int player) {
		int op = player == 1 ? 2 : 1;	//	player의 상대방
		int opRes = U;	//	승부가 나지 않은 상태
		
		for(int y = 0; y < 3; y++) {
			for(int x = 0; x < 3; x++) {
				if(board[y][x] == 0) {	//	놓을 수 있는 칸인 경우
					board[y][x] = player;
					
					if(canWin(player))	//	(y, x)에 놓으면 이길 수 있는 경우
						opRes = L;		//	상대는 반드시 짐
					
					//	 player가 (y, x)에 놓았을 때 발생하는 승/패
					int res = dfs(op);
					
					opRes = Math.min(opRes, res);
					
					board[y][x] = 0;
				}
			}
		}
		
		return opRes == L ? W : (opRes == D || opRes == U ? D : L);
	}
	
	//	현재 board 상태에서 player가 이길 수 있을 경우 true
	private static boolean canWin(int player) {
		for(int i = 0; i < 3; i++) {
			if(board[i][0] == player && board[i][0] == board[i][1] && board[i][1] == board[i][2])
				return true;
			if(board[0][i] == player && board[0][i] == board[1][i] && board[1][i] == board[2][i])
				return true;
		}
		
		if(board[0][0] == player && board[0][0] == board[1][1] && board[1][1] == board[2][2])
			return true;
		if(board[0][2] == player && board[0][2] == board[1][1] && board[1][1] == board[2][0])
			return true;
		
		return false;
	}
} //	Main-class-end
```