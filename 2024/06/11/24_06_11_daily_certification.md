# 24_06_11_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 4577 소코반

[](https://www.acmicpc.net/problem/4577)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final char WALL = '#';
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer tokens;
	
	private static int R = 0;	//	행 개수
	private static int C = 0;	//	열 개수
	private static int tc = 0;	//	테스트 케이스 번호
	
	private static char[][] map;			//	맵
	private static char[] cmdList;				//	이동 명령어
	private static final List<int[]> targetPosList = new ArrayList<>();
	
	private static int wy = 0;	//	캐릭터 y 좌표
	private static int wx = 0;	//	캐릭터 x 좌표
	
	public static void main(String[] args) throws IOException {
		while(true) {
			tokens = new StringTokenizer(br.readLine());
			R = Integer.parseInt(tokens.nextToken());
			C = Integer.parseInt(tokens.nextToken());
			
			if(R == 0 && C == 0) {	//	테스트 케이스 종료
				System.out.print(sb);
				break;
			}
			
			map = new char[R][C];
			targetPosList.clear();
			
			//	map 입력
			for(int r = 0; r < R; r++) {
				map[r] = br.readLine().toCharArray();
				for(int c = 0; c < C; c++) {
					char ch = map[r][c];
					
					if(ch == '+' || ch == 'B' || ch == 'W') {		//	목표 지점일 경우
						targetPosList.add(new int[] { r, c } );		//	목표 지점 위치 저장
						
						if(ch == 'W') {	//	목표 지점 위에 캐릭터가 있을 경우
							wy = r;
							wx = c;
						}
					}
					else if(ch == 'w') {	//	목표 지점이 아닌 곳에 캐릭터가 있을 경우
						wy = r;
						wx = c;
					}
				}
			}
			
			cmdList = br.readLine().toCharArray();
			
			boolean complete = false;
			for(char cmd : cmdList) {
				move(cmd);		//	cmd 명령어로 이동
				if(check()) {	//	이동 결과 모든 상자가 뒤덮였으면
					complete = true;	//	complete임, 게임 종료
					break;
				}
			}
			
			sb.append(String.format("Game %d: %s\n", ++tc, complete ? "complete" : "incomplete"));
			
			for(int r = 0; r < R; r++) {
				for(int c = 0; c < C; c++)
					sb.append(map[r][c]);
				sb.append("\n");
			}
		}
	}	//	main-end
	
	private static boolean check() {	//	게임이 끝났으면 true
		for(int[] targetPos : targetPosList) {
			int r = targetPos[0];
			int c = targetPos[1];
			
			if(map[r][c] != 'B')	//	아직 목표 지점이 박스로 덮이지 않았으면
				return false;
		}
		
		return true;
	}
	
	//	cmd 방향으로 캐릭터가 움직임
	private static void move(char cmd) {
		int dir = cmd == 'R' ? 0 : (cmd == 'D' ? 1 : (cmd == 'L' ? 2 : 3));
		
		int nwy = wy + dy[dir];
		int nwx = wx + dx[dir];	//	캐릭터가 이동하려는 다음 칸
		
		if(!canGo(nwy, nwx))	//	다음 칸이 벽일 경우 무시
			return;
		
		if(map[nwy][nwx] == '.') {	//	다음 칸이 빈 칸일 경우
			if(map[wy][wx] == 'W')	//	현재 캐릭터가 목표 지점에 있었을 경우
				map[wy][wx] = '+';	//	목표 지점으로 변경
			else
				map[wy][wx] = '.';	//	빈 칸으로 변경
			
			map[nwy][nwx] = 'w';	//	캐릭터가 빈 칸으로 이동
			
			wy = nwy;
			wx = nwx;	//	캐릭터가 다음 칸으로 단순히 이동
			return;		//	종료
		}
		else if(map[nwy][nwx] == '+') {	//	다음 칸이 목표 지점일 경우
			if(map[wy][wx] == 'W')	//	현재 캐릭터가 목표 지점에 있었을 경우
				map[wy][wx] = '+';	//	목표 지점으로 변경
			else
				map[wy][wx] = '.';	//	빈 칸으로 변경
			
			map[nwy][nwx] = 'W';	//	캐릭터가 목표 지점으로 이동
			
			wy = nwy;
			wx = nwx;	//	캐릭터가 다음 칸으로 단순히 이동
			return;		//	종료
		}
		else if(map[nwy][nwx] == 'b') {	//	다음 칸이 목표점이 아닌 박스일 경우
			int nnwy = nwy + dy[dir];
			int nnwx = nwx + dx[dir];	//	박스의 다음 칸
			
			//	박스의 다음 칸이 벽이거나, 박스의 다음 칸도 박스일 경우
			if(!canGo(nnwy, nnwx) || map[nnwy][nnwx] == 'b' || map[nnwy][nnwx] == 'B')
				return;	//	아무 일도 일어나지 않음
			
			//	플레이어가 다음 칸으로 이동할 수 있을 경우
			
			//	박스 이동 시키기
			if(map[nnwy][nnwx] == '+')	// 박스의 다음 칸이 목적 지점일 경우
				map[nnwy][nnwx] = 'B';
			else						//	박스의 다음 칸이 단순 빈 칸일 경우
				map[nnwy][nnwx] = 'b';
		
			if(map[wy][wx] == 'W')	//	캐릭터가 있던 곳이 목표 지점이었을 경우
				map[wy][wx] = '+';
			else					//	캐릭터가 있던 곳이 그냥 빈칸이었을 경우
				map[wy][wx] = '.';
			
			map[nwy][nwx] = 'w';	//	박스가 있던 곳은 목표 지점이 아니었음
			
			wy = nwy;
			wx = nwx;	//	박스를 민 캐릭터가 다음 칸으로 이동
			
			return;
		}
		else if(map[nwy][nwx] == 'B') {	//	다음 칸이 목표점 위의 박스일 경우
			int nnwy = nwy + dy[dir];
			int nnwx = nwx + dx[dir];	//	박스의 다음 칸
			
			//	박스의 다음 칸이 벽이거나, 박스의 다음 칸도 박스일 경우
			if(!canGo(nnwy, nnwx) || map[nnwy][nnwx] == 'b' || map[nnwy][nnwx] == 'B')
				return;	//	아무 일도 일어나지 않음
			
			//	플레이어가 다음 칸으로 이동할 수 있을 경우
			
			//	박스 이동 시키기
			if(map[nnwy][nnwx] == '+')	// 박스의 다음 칸이 목적 지점일 경우
				map[nnwy][nnwx] = 'B';
			else						//	박스의 다음 칸이 단순 빈 칸일 경우
				map[nnwy][nnwx] = 'b';
		
			if(map[wy][wx] == 'W')	//	캐릭터가 있던 곳이 목표 지점이었을 경우
				map[wy][wx] = '+';
			else					//	캐릭터가 있던 곳이 그냥 빈칸이었을 경우
				map[wy][wx] = '.';
			
			map[nwy][nwx] = 'W';	//	박스가 있던 곳은 목표 지점이었음
			
			wy = nwy;
			wx = nwx;	//	박스를 민 캐릭터가 다음 칸으로 이동
			
			return;
		}
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	//	캐릭터가 (y, x)로 갈 수 있으면, 즉 벽이 아니면 true, 벽이면 무시
	private static boolean canGo(int y, int x) {
		return map[y][x] != WALL;
	}
}	//	Main-class-end
```