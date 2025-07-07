# 25_07_07_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 17470 배열 돌리기 5

[17470번: 배열 돌리기 5](http://boj.ma/17470/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	private static int R;
	
	private static int[][] sect1;
	private static int[][] sect2;
	private static int[][] sect3;
	private static int[][] sect4;
	
	private static final int[][] board = {{11, 12, 21, 22}, {13, 14, 23, 24}, {41, 42, 31, 32}, {43, 44, 33, 34}};
	private static final int[][] tmpArr = new int[4][4];
	
	private static int[][] answer;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		R = Integer.parseInt(st.nextToken());
		
		sect1 = new int[N / 2][M / 2];
		sect2 = new int[N / 2][M / 2];
		sect3 = new int[N / 2][M / 2];
		sect4 = new int[N / 2][M / 2];
		
		for(int r = 0; r < N / 2; r++) {
			st = new StringTokenizer(br.readLine());
			for(int c = 0; c < M / 2; c++)
				sect1[r][c] = Integer.parseInt(st.nextToken());
			for(int c = 0; c < M / 2; c++)
				sect2[r][c] = Integer.parseInt(st.nextToken());
		}
		
		for(int r = 0; r < N / 2; r++) {
			st = new StringTokenizer(br.readLine());
			for(int c = 0; c < M / 2; c++)
				sect4[r][c] = Integer.parseInt(st.nextToken());
			for(int c = 0; c < M / 2; c++)
				sect3[r][c] = Integer.parseInt(st.nextToken());
		}
		
		st = new StringTokenizer(br.readLine());
		for(int r = 0; r < R; r++) {
			int opt = Integer.parseInt(st.nextToken());
			rotate(opt);
		}
		
		if(Math.abs(board[0][0] % 10 - board[0][1] % 10) == 1)
			answer = new int[N][M];
		else
			answer = new int[M][N];
		
		for(int sect = 0; sect < 4; sect++) {
			int sy = sect < 2 ? 0 : 2;
			int sx = (sect == 0 || sect == 3) ? 0 : 2;
			
			int num = board[sy][sx] / 10;
			int[][] target = num == 1 ? sect1 : (num == 2 ? sect2 : (num == 3 ? sect3 : sect4));
			
			int[][] rotated = null;
			
			int num1 = board[sy][sx] % 10;
			int num2 = board[sy][sx + 1] % 10;
			int num3 = board[sy + 1][sx] % 10;
			
			if(num1 == 1) {
				if(num2 == 2 && num3 == 3)			//	그대로
					rotated = target;
				else {
					rotated = rotate(target, 4);	//	왼쪽으로 회전
					rotated = rotate(rotated, 1);	//	상하 뒤집기
				}
			}
			else if(num1 == 2) {
				if(num2 == 1 && num3 == 4)			//	좌우 뒤집기
					rotated = rotate(target, 2);
				else								//	왼쪽으로 회전
					rotated = rotate(target, 4);
			}
			else if(num1 == 3) {
				if(num2 == 1 && num3 == 4)			//	오른쪽으로 회전
					rotated = rotate(target, 3);
				else								//	상하 뒤집기
					rotated = rotate(target, 1);
			}
			else {
				if(num2 == 2 && num3 == 3) {
					rotated = rotate(target, 4);	//	왼쪽으로 회전
					rotated = rotate(rotated, 2);	//	좌우 뒤집기
				}
				else {
					rotated = rotate(target, 1);	//	상하 뒤집기
					rotated = rotate(rotated, 2);	//	좌우 뒤집기
				}
			}
			
			int row = answer.length;
			int col = answer[0].length;
			
			sy = (sect == 0 || sect == 1) ? 0 : row / 2;
			sx = (sect == 0 || sect == 3) ? 0 : col / 2;
			
			int ey = sy + row / 2 - 1;
			int ex = sx + col / 2 - 1;
			
			for(int y = sy; y <= ey; y++) {
				for(int x = sx; x <= ex; x++)
					answer[y][x] = rotated[y - sy][x - sx];
			}
		}
		
		for(int y = 0; y < answer.length; y++) {
			for(int x = 0; x < answer[0].length; x++)
				sb.append(answer[y][x]).append(" ");
			sb.append("\n");
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static int[][] rotate(int[][] sect, int opt) {
		int row = sect.length;
		int col = sect[0].length;
		
		if(opt == 1 || opt == 2) {
			if(opt == 1) {
				for(int r = 0; r < row / 2; r++) {
					for(int c = 0; c < col; c++) {
						int tmp = sect[r][c];
						sect[r][c] = sect[row - 1 - r][c];
						sect[row - 1 -r ][c] = tmp;
					}
				}
			}
			else {
				for(int c = 0; c < col / 2; c++) {
					for(int r = 0; r < row; r++) {
						int tmp = sect[r][c];
						sect[r][c] = sect[r][col - 1 - c];
						sect[r][col - 1 - c] = tmp;
					}
				}
			}
			
			return sect;
		}
		else {
			int[][] res = new int[col][row];
			
			if(opt == 3) {
				for(int r = 0; r < row; r++) {
					for(int c = 0; c < col; c++)
						res[c][row - r - 1] = sect[r][c];
				}
			}
			else {
				for(int r = 0; r < row; r++) {
					for(int c = 0; c < col; c++)
						res[col - 1 - c][r] = sect[r][c];
				}
			}
			
			return res;
		}
	}
	
	private static void rotate(int opt) {
		if(opt == 1) {	//	상하 뒤집기
			for(int r = 0; r < 2; r++) {
				for(int c = 0; c < 4; c++) {
					int tmp = board[r][c];
					board[r][c] = board[3 - r][c];
					board[3 - r][c] = tmp;
				}
			}
		}
		else if(opt == 2) {	//	좌우 뒤집기
			for(int c = 0; c < 2; c++) {
				for(int r = 0; r < 4; r++) {
					int tmp = board[r][c];
					board[r][c] = board[r][3 - c];
					board[r][3 - c] = tmp;
				}
			}
		}
		else {
			if(opt == 3) {	//	오른쪽 90도 회전
				for(int r = 0; r < 4; r++) {
					for(int c = 0; c < 4; c++)
						tmpArr[c][3 - r] = board[r][c];
				}
			}
			else if(opt == 4) {	//	왼쪽 90도 회전
				for(int r = 0; r < 4; r++) {
					for(int c = 0; c < 4; c++)
						tmpArr[3 - c][r] = board[r][c];
				}
			}
			else if(opt == 5 || opt == 6) {	//	사분면 단위로 오른쪽/왼쪽으로 한 사분면씩 이동
				for(int csect = 0; csect < 4; csect++) {
					int cy = csect < 2 ? 0 : 2;
					int cx = (csect == 0 || csect == 3) ? 0 : 2;
					int nsect = (opt == 5 ? csect + 1 : csect - 1 + 4) % 4;
					int ny = nsect < 2 ? 0 : 2;
					int nx = (nsect == 0 || nsect == 3) ? 0 : 2;
					
					for(int r = 0; r < 2; r++) {
						for(int c = 0; c < 2; c++)
							tmpArr[ny + r][nx + c] = board[cy + r][cx + c];
					}
				}
			}
			
			for(int r = 0; r < 4; r++) {
				for(int c = 0; c < 4; c++)
					board[r][c] = tmpArr[r][c];
			}
		}
	}
} //	Main-class-end
```