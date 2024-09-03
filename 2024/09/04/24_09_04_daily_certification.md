# 24_09_04_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 19235 모노미노도미노

[](https://www.acmicpc.net/problem/19235)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.HashSet;
import java.util.List;
import java.util.Map;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
	private static final int EMPTY = 0;	//	블록이 놓이지 않은 경우
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static final int[][] blue = new int[4][6];		//	blue[r][c] : blue의 (r, c)에 놓인 블록 번호 (1 ~ N)
	private static final int[][] green = new int[6][4];		//	green[r][c] : green의 (r, c)에 놓인 블록 번호 (1 ~ N)
	
	private static int N;	//	블록 놓는 횟수
	private static int ans;	//	점수 (blue에서 한 열이 완성될 때, 혹은 green에서 한 행이 완성될 때, 1점씩 증가)
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		for(int blockNum = 1; blockNum <= N; blockNum++) {
			st = new StringTokenizer(br.readLine());
			int t = Integer.parseInt(st.nextToken());	//	블록 종류
			int x = Integer.parseInt(st.nextToken());
			int y = Integer.parseInt(st.nextToken());	//	블록 놓을 좌표 (x, y)
			
			putBlue(blockNum, t, x);
			putGreen(blockNum, t, y);	//	블록 놓기
			
			addBlueScore();
			addGreenScore();	//	추가되는 점수 있나 확인하기
			
			moveRight();
			moveDown();			//	연한 칸 블록 제거
		}
		
		System.out.println(ans);
		System.out.println(remainBlocks());
	}	//	main-end
	
	//	파란 블록을 일단 최대한 오른쪽으로 이동시키기
	private static void putBlue(int blockNum, int block, int x) {
		int y = 2;
		
		if(block == 1 || block == 2) {
			while(y < 6 && blue[x][y] == EMPTY)
				y++;
			
			if(block == 2)
				blue[x][y - 2] = blockNum;
			blue[x][y - 1] = blockNum;
		}
		else {
			while(y < 6 && blue[x][y] == EMPTY && blue[x + 1][y] == EMPTY)
				y++;
			
			blue[x][y - 1] = blockNum;
			blue[x + 1][y - 1] = blockNum;
		}
	}
	
	//	초록 블록을 일단 최대한 아래쪽으로 이동시키기
	private static void putGreen(int blockNum, int block, int y) {
		int x = 2;
		
		if(block == 1 || block == 3) {
			while(x < 6 && green[x][y] == EMPTY)
				x++;
			
			if(block == 3)
				green[x - 2][y] = blockNum;
			green[x - 1][y] = blockNum;
		}
		else {
			while(x < 6 && green[x][y] == EMPTY && green[x][y + 1] == EMPTY)
				x++;
			
			green[x - 1][y] = blockNum;
			green[x - 1][y + 1] = blockNum;
		}
	}
	
	//	blue의 2 ~ 5 열을 확인해서 4칸 모두 찬 열이 있으면 해당 열 1점 추가 후, 해당 열 지우기
	private static void addBlueScore() {
		while(true){
			int add = 0;	//	현재 상태에서 얻을 점수
			
			for(int col = 2; col < 6; col++) {
				int filled = 0;	//	col 열에서 채워진 칸 수
				
				for(int row = 0; row < 4; row++) {
					if(blue[row][col] != EMPTY)
						filled++;
					else 	//	col열에는 빈 칸이 있음
						break;
				}
				
				if(filled == 4) {	//	col열이 꽉 찼을 경우
					add++;	//	얻을 점수 1점 추가
					clearBlueLine(col);	//	col열 지우기
				}
			}
			
			if(add > 0)	{	//	지워진 열이 있을 경우
				ans += add;		//	지워진 열 수만큼 점수 추가
				pushRight();		//	열 지우고 블록 오른쪽으로 이동시키고 다시 확인하기
			}
			else break;
		}
	}
	
	// green의 2 ~ 5 행을 확인해서 4칸 모두 찬 행이 있으면 해당 행 1점 추가 후, 해당 행 지우기
	private static void addGreenScore() {
		while(true){
			int add = 0;	//	현재 상태에서 얻을 점수
			
			for(int row = 2; row < 6; row++) {
				int filled = 0;	//	row 행에서 채워진 칸 수
				
				for(int col = 0; col < 4; col++) {
					if(green[row][col] != EMPTY)
						filled++;
					else 	//	row행에는 빈 칸이 있음
						break;
				}
				
				if(filled == 4) {	//	row행이 꽉 찼을 경우
					add++;	//	얻을 점수 1점 추가
					clearGreenLine(row);	//	row행 지우기
				}
			}
			
			if(add > 0)	{	//	지워진 행이 있을 경우
				ans += add;		//	지워진 행 수만큼 점수 추가
				pushDown();		//	행 지우고 블록 내리고 다시 확인하기
			}
			else break;
		}
	}
	
	private static void clearBlueLine(int col) {
		for(int row = 0; row < 4; row++)
			blue[row][col] = EMPTY;
	}
	
	private static void clearGreenLine(int row) {
		for(int col = 0; col < 4; col++)
			green[row][col] = EMPTY;
	}
	
	private static void pushRight() {
		Map<Integer, Block> blockMap = new HashMap<>();		
		Set<Integer> blockNumSet = new HashSet<>();			//	현재 blue 칸에 존재하는 블록 번호들
		List<Integer> blockNumList = new ArrayList<>();		//	현재 blue 칸의 오른쪽부터 존재하는 블록 번호들, 이 순서대로 최대한 오른쪽으로 이동할 것임
		
		for(int c = 5; c >= 0; c--) {
			for(int r = 0; r < 4; r++) {
				int blockNum = blue[r][c];
				if(blockNum != EMPTY) {	//	해당 칸에 블록이 존재할 경우
					if(!blockNumSet.contains(blockNum)) {	//	최초로 나왔을 경우
						List<int[]> posList = new ArrayList<>();
						posList.add(new int[] {r, c});
						blockMap.put(blockNum, new Block(posList));
						blockNumSet.add(blockNum);
						blockNumList.add(blockNum);
					}
					else {	//	이미 이전에 나온 블록과 같은 블록이 또 있을 경우
						List<int[]> posList = blockMap.get(blockNum).posList;
						posList.add(new int[] {r, c});
					}
				}
			}
		}
		
		for(int blockNum : blockNumList) {	//	오른쪽에 있던 블록부터 오른쪽으로 이동할 수 있을 때까지 이동하기
			Block block = blockMap.get(blockNum);	//	오른쪽으로 이동할 블록
			
			if(block.posList.size() == 2) {	//	떨어뜨릴 블록이 2개로 이루어져 있을 경우
				int cx1 = block.posList.get(0)[0];
				int cy1 = block.posList.get(0)[1];	//	떨어뜨릴 블록의 첫번째 칸의 현재 위치
				int cx2 = block.posList.get(1)[0];
				int cy2 = block.posList.get(1)[1];	//	떨어뜨릴 블록의 두번째 칸의 현재 위치
				
				int ny = EMPTY;
				if(cy1 == cy2) {	//	둘이 나란히 오른쪽으로 갈 경우
					ny = cy1 + 1;
					
					while(ny < 6 && blue[cx1][ny] == EMPTY && blue[cx2][ny] == EMPTY)
						ny++;
					
					ny--;
					
					blue[cx1][cy1] = EMPTY;
					blue[cx2][cy2] = EMPTY;
					blue[cx1][ny] = blockNum;
					blue[cx2][ny] = blockNum;
				}
				else {	//	둘이 가로로 떨어질 경우
					int maxY = Math.max(cy1, cy2);
					ny = maxY + 1;
					
					while(ny < 6 && blue[cx1][ny] == EMPTY)
						ny++;
					
					ny--;
					
					blue[cx1][cy1] = EMPTY;
					blue[cx2][cy2] = EMPTY;
					blue[cx1][ny - 1] = blockNum;
					blue[cx2][ny] = blockNum;
				}
			}
			else {	//	떨어뜨릴 블록이 1개로 이루어져 있을 경우
				int cx = block.posList.get(0)[0];
				int cy = block.posList.get(0)[1];	//	떨어뜨릴 블록의 현재 위치
				
				int ny = cy + 1;
				
				while(ny < 6 && blue[cx][ny] == EMPTY)
					ny++;
				
				ny--;
				
				blue[cx][cy] = EMPTY;
				blue[cx][ny] = blockNum;
			}
		}
	}
	
	private static void pushDown() {
		Map<Integer, Block> blockMap = new HashMap<>();		
		Set<Integer> blockNumSet = new HashSet<>();			//	현재 green 칸에 존재하는 블록 번호들
		List<Integer> blockNumList = new ArrayList<>();		//	현재 green 칸의 아래부터 존재하는 블록 번호들, 이 순서대로 최대한 내려갈 것임
		
		for(int r = 5; r >= 0; r--) {
			for(int c = 0; c < 4; c++) {
				int blockNum = green[r][c];
				if(blockNum != EMPTY) {	//	해당 칸에 블록이 존재할 경우
					if(!blockNumSet.contains(blockNum)) {	//	최초로 나왔을 경우
						List<int[]> posList = new ArrayList<>();
						posList.add(new int[] {r, c});
						blockMap.put(blockNum, new Block(posList));
						blockNumSet.add(blockNum);
						blockNumList.add(blockNum);
					}
					else {	//	이미 이전에 나온 블록과 같은 블록이 또 있을 경우
						List<int[]> posList = blockMap.get(blockNum).posList;
						posList.add(new int[] {r, c});
					}
				}
			}
		}
		
		for(int blockNum : blockNumList) {	//	아래에 있던 블록부터 내려갈 수 있을 때까지 내려가기
			Block block = blockMap.get(blockNum);	//	떨어뜨릴 블록
			
			if(block.posList.size() == 2) {	//	떨어뜨릴 블록이 2개로 이루어져 있을 경우
				int cx1 = block.posList.get(0)[0];
				int cy1 = block.posList.get(0)[1];	//	떨어뜨릴 블록의 첫번째 칸의 현재 위치
				int cx2 = block.posList.get(1)[0];
				int cy2 = block.posList.get(1)[1];	//	떨어뜨릴 블록의 두번째 칸의 현재 위치
				
				int nx = EMPTY;
				if(cx1 == cx2) {	//	둘이 나란히 떨어질 경우
					nx = cx1 + 1;
					
					while(nx < 6 && green[nx][cy1] == EMPTY && green[nx][cy2] == EMPTY)
						nx++;
					
					nx--;
					
					green[cx1][cy1] = EMPTY;
					green[cx2][cy2] = EMPTY;
					green[nx][cy1] = blockNum;
					green[nx][cy2] = blockNum;
				}
				else {	//	둘이 세로로 떨어질 경우
					int maxX = Math.max(cx1, cx2);
					nx = maxX + 1;
					
					while(nx < 6 && green[nx][cy1] == EMPTY)
						nx++;
					
					nx--;
					
					green[cx1][cy1] = EMPTY;
					green[cx2][cy2] = EMPTY;
					green[nx - 1][cy1] = blockNum;
					green[nx][cy2] = blockNum;
				}
			}
			else {	//	떨어뜨릴 블록이 1개로 이루어져 있을 경우
				int cx = block.posList.get(0)[0];
				int cy = block.posList.get(0)[1];	//	떨어뜨릴 블록의 현재 위치
				
				int nx = cx + 1;
				
				while(nx < 6 && green[nx][cy] == EMPTY)
					nx++;
				
				nx--;
				
				green[cx][cy] = EMPTY;
				green[nx][cy] = blockNum;
			}
		}
	}
	
	private static void moveDown() {
		int cnt = 0;	//	아래쪽으로 이동할 칸 개수
		
		for(int r = 0; r < 2; r++) {
			for(int c = 0; c < 4; c++) {
				if(green[r][c] != EMPTY) {
					cnt++;
					break;
				}
			}
		}
		
		for(int i = 0; i < cnt; i++) {
			for(int r = 5; r >= 1; r--) {
				for(int c = 0; c < 4; c++)
					green[r][c] = green[r - 1][c];
			}
			for(int c = 0; c < 4; c++)
				green[0][c] = EMPTY;
		}
	}
	
	private static void moveRight() {
		int cnt = 0;	//	오른쪽으로 이동할 칸 개수
		
		for(int c = 0; c < 2; c++) {
			for(int r = 0; r < 4; r++) {
				if(blue[r][c] != EMPTY) {
					cnt++;
					break;
				}
			}
		}
		
		for(int i = 0; i < cnt; i++) {
			for(int c = 5; c >= 1; c--) {
				for(int r = 0; r < 4; r++)
					blue[r][c] = blue[r][c - 1];
			}
			for(int r = 0; r < 4; r++)
				blue[r][0] = EMPTY;
		}
	}
	
	private static int remainBlocks() {
		int bCnt = 0;	//	최종 상태에서 blue 중 블록이 차지한 칸 개수
		int gCnt = 0;	//	최종 상태에서 green 중 블록이 차지한 칸 개수
		
		for(int r = 0; r < 4; r++) {
			for(int c = 2; c < 6; c++)
				if(blue[r][c] != EMPTY)
					bCnt++;
		}
		for(int r = 2; r < 6; r++) {
			for(int c = 0; c < 4; c++)
				if(green[r][c] != EMPTY)
					gCnt++;
		}
		
		return bCnt + gCnt;
	}
	
	private static class Block {
		public List<int[]> posList;
		
		public Block(List<int[]> posList) {
			this.posList = posList;
		}
	}
}	//	Main-class-end
```