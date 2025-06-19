# 25_06_19_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16989 Baaaaaaaaaduk2 (Hard)

[16989번: Baaaaaaaaaduk2 (Hard)](http://boj.ma/16989/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.HashSet;
import java.util.LinkedList;
import java.util.List;
import java.util.Map;
import java.util.Objects;
import java.util.Queue;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	
	private static int[][] board;
	private static int[][] ccNum;
	
	private static int ccCnt = 0;											//	흑돌로 이루어진 연결요소 개수
	private static final List<Integer> ccSizes = new ArrayList<>();			//	ccSizes[i] : i번째 연결요소에 포함된 흑돌 개수
	private static final List<Integer> ccBlankCnts = new ArrayList<>();		//	ccBlankCnts[i] : i번째 연결요소와 인접한 빈칸 개수
	
	//	Key : 인접한 빈칸이 2개 이하인 흑돌 연결요소 번호
	//	Value : 그 연결요소에 인접한 빈칸 좌표 리스트
	private static final Map<Integer, List<Pos>> ccMap = new HashMap<>();
	
	//	Key : 빈칸 pos
	//	Value : pos 1칸만 막으면 잡을 수 있는 흑돌 연결요소 번호 리스트
	private static final Map<Pos, List<Integer>> posMap = new HashMap<>();
	
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		board = new int[N][M];
		ccNum = new int[N][M];
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < M; x++)
				board[y][x] = Integer.parseInt(st.nextToken());
		}
		
		ccSizes.add(-1);
		ccBlankCnts.add(-1);
		
		for(int y = 0; y < N; y++) {
			for(int x = 0; x < M; x++) {
				if(board[y][x] == 2 && ccNum[y][x] == 0) {
					ccCnt++;
					bfs(y, x);
				}
			}
		}
		for(int num : ccMap.keySet()) {
			List<Pos> blankList = ccMap.get(num);
			
			if(blankList.size() == 2) {//	빈칸 2개를 막는 경우
				int total = ccSizes.get(num);	//	해당 빈칸 2개를 막았을때 잡을 수 있는 흑돌 개수, 일단 num 연결요소의 흑돌은 모두 잡음
				
				Pos pos1 = blankList.get(0);
				Pos pos2 = blankList.get(1);
				
				Set<Integer> ccSet = new HashSet<>();	//	pos1, pos2와 이웃한 연결요소
				for(int d = 0; d < 4; d++) {
					int y = pos1.y + dy[d];
					int x = pos1.x + dx[d];
					
					if(isIn(y, x) && 1 <= ccNum[y][x] && ccNum[y][x] <= N * M ) {
						int nnum = ccNum[y][x];
						
						if(ccBlankCnts.get(nnum) <= 2 && nnum != num)	//	인접하면서 막을 수도 있는 연결요소일 경우
							ccSet.add(nnum);
					}
				}
				
				for(int d = 0; d < 4; d++) {
					int y = pos2.y + dy[d];
					int x = pos2.x + dx[d];
					
					if(isIn(y, x) && 1 <= ccNum[y][x] && ccNum[y][x] <= N * M ) {
						int nnum = ccNum[y][x];
						
						if(ccBlankCnts.get(nnum) <= 2 && nnum != num)	//	인접하면서 막을 수도 있는 연결요소일 경우
							ccSet.add(nnum);
					}
				}
				
				for(int nnum : ccSet) {
					if(nnum == num)
						continue;
					
					List<Pos> nBlankList = ccMap.get(nnum);
					
					Pos npos1 = nBlankList.get(0);
					
					if(nBlankList.size() == 1) {
						if(npos1.equals(pos1) || npos1.equals(pos2))
							total += ccSizes.get(nnum);
					}
					else {
						Pos npos2 = nBlankList.get(1);
						
						if((npos1.equals(pos1) && npos2.equals(pos2)) || (npos1.equals(pos2) && npos2.equals(pos1)))
							total += ccSizes.get(nnum); 
					}
					
				}
				
				answer = Math.max(answer, total);
			}
			else {	//	막아야 하는 빈칸이 하나인 경우
				Pos pos = blankList.get(0);		//	num 연결요소를 잡기 위해 막아야 하는 한개의 점
				int total = ccSizes.get(num);	//	pos를 막는다고 가정할때 잡을 수 있는 흑돌 최대 개수
				int add = 0;

				//	막아야 하는 빈칸이 2개인데 pos로 인해 1개가 막아지는 연결요소들
				Set<Integer> ccSet = new HashSet<>();
				
				for(int d = 0; d < 4; d++) {
					int y = pos.y + dy[d];
					int x = pos.x + dx[d];
					
					if(isIn(y, x) && 1 <= ccNum[y][x] && ccNum[y][x] <= N * M ) {
						int nnum = ccNum[y][x];
						
						if(nnum == num)
							continue;
						
						if(ccBlankCnts.get(nnum) == 1)	//	pos로 인해 완전히 박힐 경우
							total += ccSizes.get(nnum);
						else if(ccBlankCnts.get(nnum) == 2)	//	pos로 인해 하나가 막혀서 나머지 하나만 막으면 될 경우
							ccSet.add(nnum);	
					}
				}
				
				//	pos 외에 추가로 하나의 빈칸 더 막는 경우
				//	pos를 공유하던 것중 하나 선택
				for(int nnum : ccSet) {
					List<Pos> nPosList = ccMap.get(nnum);
					
					if(nPosList.size() == 1)
						continue;
					
					Pos npos1 = nPosList.get(0);
					Pos npos2 = null;
					try {
						npos2 = nPosList.get(1);
					} catch(Exception e) {
						System.exit(-1);
					}
					Pos npos = npos1.equals(pos) ? npos2 : npos1;	//	pos 외에 더 막을 빈칸 하나
					int nadd = 0;
					
					for(int d = 0; d < 4; d++) {
						int y = npos.y + dy[d];
						int x = npos.x + dx[d];
						
						if(isIn(y, x) && 1 <= ccNum[y][x] && ccNum[y][x] <= N * M ) {
							int nnnum = ccNum[y][x];	//	새로 막을 점 npos와 인접한 연결요소
							
							if(nnnum == num || nnnum == nnum)
								continue;
							
							if(ccBlankCnts.get(nnnum) == 1)	//	npos만으로 막아질 경우
								nadd = Math.max(nadd, ccSizes.get(nnnum));
						}
					}
					
					add = Math.max(add, ccSizes.get(nnum) + nadd);
				}
				
				//	pos를 공유하지 않던 것중 하나 선택
				for(Pos other : posMap.keySet()) {
					if(other.equals(pos))
						continue;
					
					List<Integer> ccList = posMap.get(other);
					
					for(int nnum : ccList)
						add = Math.max(add, ccSizes.get(nnum));
				}
				
				answer = Math.max(answer, total + add);
			}
		}
		
		System.out.println(answer);
	} //	main-end
	
	private static void bfs(int y, int x) {
		Queue<Pos> queue = new LinkedList<>();
		
		int cntBlack = 1;	//	(y, x)가 포함된 cc에 포함된 흑돌 개수	
		int cntBlank = 0;	//	(y, x)가 포함된 cc와 인접한 빈칸 개수
		int blankKey = N * M + ccCnt;
		
		List<Pos> blankPosList = new ArrayList<>();
		
		ccNum[y][x] = ccCnt;
		queue.offer(new Pos(y, x));
		
		while(!queue.isEmpty()) {
			Pos cur = queue.poll();
			int cy = cur.y;
			int cx = cur.x;
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				
				if(isIn(ny, nx)) {
					if(board[ny][nx] == 0 && ccNum[ny][nx] != blankKey) {
						cntBlank++;
						ccNum[ny][nx] = blankKey;
						blankPosList.add(new Pos(ny, nx));
					}
					else if(board[ny][nx] == 2 && ccNum[ny][nx] == 0) {
						cntBlack++;
						ccNum[ny][nx] = ccCnt;
						queue.offer(new Pos(ny, nx));
					}
				}
			}
		}
		
		ccSizes.add(cntBlack);
		ccBlankCnts.add(cntBlank);
		
		if(blankPosList.size() <= 2) {
			ccMap.put(ccCnt, blankPosList);
			if(blankPosList.size() == 1) {
				Pos pos = blankPosList.get(0);
				List<Integer> ccList = posMap.getOrDefault(pos, new ArrayList<>());
				ccList.add(ccCnt);
				posMap.put(pos, ccList);
			}
		}
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}
	
	private static class Pos {
		public int y;
		public int x;
		
		public Pos(int y, int x) {
			this.y = y;
			this.x = x;
		}
		
		@Override
		public boolean equals(Object obj) {
			if(obj == this)
				return true;
			
			if(obj == null || obj.getClass() != getClass())
				return false;
			
			Pos pos = (Pos)obj;
			
			return this.y == pos.y && this.x == pos.x;
		}
	
		@Override
		public int hashCode() {
			return Objects.hash(this.y, this.x);
		}
	}
} //	Main-class-end
```