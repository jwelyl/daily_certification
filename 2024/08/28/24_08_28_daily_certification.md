# 24_08_27_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 15778 Yut Nori

[](https://www.acmicpc.net/problem/15778)

```java
import java.util.*;
import java.io.*;

public class Main {
	private static final char[][] board = new char[32][32];

	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	//	luyPos[pos] : 현재 말이 pos칸에 있을 때, 대응되는 2 * 2 board 칸의 왼쪽 위 y 좌표
	private static final int[] luyPos = {
			// 0 ~ 20
			30, 24, 18, 12, 6, 0, 0, 0, 0, 0, 0, 6, 12, 18, 24, 30, 30, 30, 30, 30, 30,
			// 21 ~ 32
			0, 5, 10, 15, 20, 25, 30, 30, 30, 30, 30, 30,
			// 33 ~ 36
			15, 20, 25, 30,
			// 37 ~ 43
			0, 5, 10, 15, 20, 25, 30 };

	//	luxPos[pos] : 현재 말이 pos칸에 있을 때, 대응되는 2 * 2 board 칸의 왼쪽 위 x 좌표
	private static final int[] luxPos = {
			// 0 ~ 20
			30, 30, 30, 30, 30, 30, 24, 18, 12, 6, 0, 0, 0, 0, 0, 0, 6, 12, 18, 24, 30,
			// 21 ~ 32
			30, 25, 20, 15, 10, 5, 0, 6, 12, 18, 24, 30,
			// 33 ~ 36
			15, 20, 25, 30,
			// 37 ~ 43
			0, 5, 10, 15, 20, 25, 30 };
	
	//	team1의 말 A, B, C, D의 현재 칸 위치
	private static final int[] team1Pos = new int[4];
	
	//	team2의 말 a, b, c, d의 현재 칸 위치
	private static final int[] team2Pos = new int[4];

	private static int N; // N번 말을 던짐

	public static void main(String[] args) throws IOException {
		init();
		
		N = Integer.parseInt(br.readLine());
		
		for(int i = 0; i < N; i++) {
			st = new StringTokenizer(br.readLine());
			char horse = st.nextToken().charAt(0);		//	현재 움직일 말
			int moveCnt = moveCnt(st.nextToken());		//	움직일 칸 수
			
			char pivot = 'A' <= horse && horse <= 'D' ? 'A' : 'a';					//	대문자면 'A'를 기준으로, 소문자면 'a'를 기준으로
			int[] teamPos = 'A' <= horse && horse <= 'D' ? team1Pos : team2Pos;		//	어느 팀에 속하는지, 대문자면 team1의 말, 소문자면 team2의 말
			int[] rivalPos = 'A' <= horse && horse <= 'D' ? team2Pos : team1Pos;	//	상태 팀
			
			int curPos = teamPos[horse - pivot];	//	현재 움직일 말 horse가 위치한 칸
			
			if(curPos == -1) {	//	이미 보드를 돌고 나간 말일 경우
				System.out.println("Something was wrong!!");
				System.exit(-1);;
			}
			
			int[] cur = setYXPos(curPos, horse, pivot);
			int cy = cur[0];
			int cx = cur[1];	//	현재 칸에 대응되는 board에서 horse가 위치한 좌표
		
			int[] next;
			int nextPos = curPos + moveCnt;			//	윳 던져서 나온 칸수 더한 칸 -> 궁극적으로 이동할 칸
			int ny = -1;
			int nx = -1;							//	이동할 칸에 대응되는 board에서 horse가 위치한 좌표, (-1, -1)일 경우 보드를 나간 것으로 여길 수 있음
			
			if(16 <= curPos && curPos <= 20) {	//	horse가 경로 4에서 나갈 수 있는 후보 위치에 있을 경우
				if(nextPos > 20) 	//	나가게 된 경우
					teamPos[horse - pivot] = -1;	//	나간 말로 처리
				else {
					next = setYXPos(nextPos, horse, pivot);
					ny = next[0];
					nx = next[1];	//	이동할 칸에 대응되는 board에서 horse가 위치한 좌표
				}
			}
			else if(27 <= curPos && curPos <= 32) {	//	horse가 경로 3에서 나갈 수 있는 후보 위치에 있을 경우
				if(nextPos > 32) 	//	나가게 된 경우
					teamPos[horse - pivot] = -1;	//	나간 말로 처리
				else {
					next = setYXPos(nextPos, horse, pivot);
					ny = next[0];
					nx = next[1];	//	이동할 칸에 대응되는 board에서 horse가 위치한 좌표
				}
			}
			else if(38 <= curPos && curPos <= 43) {	//	horse가 경로 2에서 나갈 수 있는 후보 위치에 있을 경우
				if(nextPos > 43) 	//	나가게 된 경우
					teamPos[horse - pivot] = -1;	//	나간 말로 처리
				else {
					next = setYXPos(nextPos, horse, pivot);
					ny = next[0];
					nx = next[1];	//	이동할 칸에 대응되는 board에서 horse가 위치한 좌표
				}
			}
			else if(33 <= curPos && curPos <= 36) {	//	horse가 경로 1에서 나갈 수 있는 후보 위치에 있을 경우
				if(nextPos > 36) 	//	나가게 된 경우
					teamPos[horse - pivot] = -1;	//	나간 말로 처리
				else {
					next = setYXPos(nextPos, horse, pivot);
					ny = next[0];
					nx = next[1];	//	이동할 칸에 대응되는 board에서 horse가 위치한 좌표
				}
			}
			else if(0 <= curPos && curPos <= 4) {
				if(nextPos == 5)	//	경로 4에서 경로 3으로 꺾게 된 경우
					nextPos = 21;
				next = setYXPos(nextPos, horse, pivot);
				ny = next[0];
				nx = next[1];	//	이동할 칸에 대응되는 board에서 horse가 위치한 좌표
			}
			else if(6 <= curPos && curPos <= 9) {
				if(nextPos == 10)	//	경로 4에서 경로 2로 꺾게 된 경우
					nextPos = 37;
				next = setYXPos(nextPos, horse, pivot);
				ny = next[0];
				nx = next[1];	//	이동할 칸에 대응되는 board에서 horse가 위치한 좌표
			}
			else if(21 <= curPos && curPos <= 23) {
				if(nextPos == 24)	//	경로 3에서 경로 1로 꺾게 된 경우
					nextPos = 33;
				next = setYXPos(nextPos, horse, pivot);
				ny = next[0];
				nx = next[1];	//	이동할 칸에 대응되는 board에서 horse가 위치한 좌표
			}
			else {	//	그 외 위치일 경우 그냥 이동하면 된다.
				next = setYXPos(nextPos, horse, pivot);
				ny = next[0];
				nx = next[1];	//	이동할 칸에 대응되는 board에서 horse가 위치한 좌표
			}
			
			if(ny != -1 && nx != -1) {	//	보드 밖으로 나가지 않았을 경우, 잡을 수 있는 말이 있는지 확인해야 함
				for(int r = 0; r < 4; r++) {	//	상대의 r번째 말 위치 확인
					int rpos = rivalPos[r];
					
					if(nextPos == rpos) {	//	상대의 r번째 말 위치가 내 말이 이동할 위치와 같다면
						int[] rival = setYXPos(rpos, r);
						int ry = rival[0];
						int rx = rival[1];
						
						rivalPos[r] = 0;		//	상대 말 아웃시킴
						board[ry][rx] = '.';	//	상대 말 보드에서 지우기
					}
				}
				
				board[ny][nx] = horse;	//	이동할 칸에 내 말 놓기
			}
			
			for(int order = 0; order < 4; order++) {
				if(order == horse - pivot)
					continue;
				
				if(teamPos[order] != 0 && teamPos[order] == curPos) {	//	같은 편 말 중 이전에 같은 칸에 있었을 경우
					int[] ccur = setYXPos(curPos, order);
					int ccy = ccur[0];
					int ccx = ccur[1];	//	같이 있던 말의 이동하기 전 보드에서의 좌표
					
					if(ny != -1 && nx != -1) {	//	보드 벗어나지 않을 경우
						int[] nnext = setYXPos(nextPos, order);
						int nny = nnext[0];
						int nnx = nnext[1];	//	같이 있는 말도 같이 이동했을 때 보드에서의 좌표
						
						board[nny][nnx] = (char)(pivot + order);
					}
					
					teamPos[order] = nextPos;
					board[ccy][ccx] = '.';	//	이동하기 전에 있던 칸에서 같이 이동한 말 지우기
				}
			}
			
			teamPos[horse - pivot] = nextPos;
			board[cy][cx] = '.';	//	이동하기 전에 있던 칸에서 현재 말 지우기
		}

		for(int i = 0; i < 32; i++) {
			for(int j = 0; j < 32; j++)
				sb.append(board[i][j]);
			sb.append("\n");
		}
		
		System.out.print(sb);
	} // main-end
	
	private static int[] setYXPos(int pos, int order) {
		int[] ret = {0, 0};
		
		ret[0] = luyPos[pos] + ((order == 0 || order == 1) ? 0 : 1);
		ret[1] = luxPos[pos] + ((order % 2 == 0) ? 0 : 1);
		
		return ret;
	}
	
	private static int[] setYXPos(int pos, char horse, char pivot) {
		return setYXPos(pos, horse - pivot);
	}

	private static void init() {
		int idx = 0;

		for (int i = 0; i < initState.length(); i++) {
			char ch = initState.charAt(i);

			if (ch == '\n')
				continue;

			board[idx / 32][idx % 32] = ch;
			idx++;
		}
	}
	
	private static int moveCnt(String yut) {
		int front = 0;
		
		for(int i = 0; i < 4; i++)
			if(yut.charAt(i) == 'F')
				front++;
		
		return front == 0 ? 5 : front;
	}

	private static final String initState 
				= "..----..----..----..----..----..\n"
				+ "..    ..    ..    ..    ..    ..\n"
				+ "| \\                          / |\n"
				+ "|  \\                        /  |\n"
				+ "|   \\                      /   |\n"
				+ "|    ..                  ..    |\n"
				+ "..   ..                  ..   ..\n"
				+ "..     \\                /     ..\n"
				+ "|       \\              /       |\n"
				+ "|        \\            /        |\n"
				+ "|         ..        ..         |\n"
				+ "|         ..        ..         |\n"
				+ "..          \\      /          ..\n"
				+ "..           \\    /           ..\n"
				+ "|             \\  /             |\n"
				+ "|              ..              |\n"
				+ "|              ..              |\n"
				+ "|             /  \\             |\n"
				+ "..           /    \\           ..\n"
				+ "..          /      \\          ..\n"
				+ "|         ..        ..         |\n"
				+ "|         ..        ..         |\n"
				+ "|        /            \\        |\n"
				+ "|       /              \\       |\n"
				+ "..     /                \\     ..\n"
				+ "..   ..                  ..   ..\n"
				+ "|    ..                  ..    |\n"
				+ "|   /                      \\   |\n"
				+ "|  /                        \\  |\n"
				+ "| /                          \\ |\n"
				+ "..    ..    ..    ..    ..    ..\n"
				+ "..----..----..----..----..----..";	 
} // Main-class-end
```