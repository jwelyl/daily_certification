# 24_09_29_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 17136 색종이 붙이기

[](https://www.acmicpc.net/problem/17136)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static final char[][] paper = new char[10][10];

	// cnts[size] : 크기가 size * size인 색종이 개수
	private static final int[] cnts = new int[] { 0, 5, 5, 5, 5, 5 };

	private static int fill = 0; // 채워야 하는 칸의 수
	private static int ans = Integer.MAX_VALUE; // 채워야 하는 모든 칸을 채우기 위해 필요한 색종이의 최소 개수

	public static void main(String[] args) throws IOException {
		for (int r = 0; r < 10; r++) {
			st = new StringTokenizer(br.readLine());
			for (int c = 0; c < 10; c++) {
				paper[r][c] = st.nextToken().charAt(0);

				if (paper[r][c] == '1')
					fill++;
			}
		}
		
		if(fill == 0)
			System.out.println(0);
		else {
			dfs(0, 0, fill);
			
			System.out.println(ans == Integer.MAX_VALUE ? -1 : ans);
		}

	} // main-end
	
	private static void dfs(int nth, int cnt, int fill) {
		if(fill == 0) {	//	모든 빈칸을 채웠을 경우
			ans = Math.min(ans, cnt);
			return;
		}
		
		if(nth == 10 * 10)	//	마지막 칸까지 왔을 경우
			return;
		
		int y = nth / 10;
		int x = nth % 10;	//	(y, x)
		
		if(paper[y][x] == '0')	//	(y, x)가 빈칸이 아닐 경우 
			dfs(nth + 1, cnt, fill);	//	다음 칸으로 넘어가기
		else {
			//	(y, x)를 좌상단 꼭짓점으로 size * size 크기 색종이 붙이기 시도
			for(int size = 1; size <= 5; size++) {
				if(cnts[size] == 0)	//	size * size 크기 색종이가 더 없을 경우
					continue;	//	다음 크기 색종이 붙이기 시도
				
				if(!canPaste(y, x, size))	//	size * size 크기 색종이를 붙일 수 없을 경우
					break;	//	더 큰 색종이는 당연히 붙일 수 없으므로 종료
				
				convert(y, x, size);	//	(y, x)를 좌상단 꼭짓점으로 size * size 크기 색종이 붙이기
				cnts[size]--;			//	size * size 크기 색종이 1개 감소
				dfs(nth + 1, cnt + 1, fill - size * size);	//	사용한 색종이 수 1 증가, 채워야 할 칸 개수 size * size개 감소
				convert(y, x, size);	//	(y, x)를 좌상단 꼭짓점으로 size * size 크기 색종이 붙인 것 떼기
				cnts[size]++;			//	size * size 크기 색종이 다시 1개 증가
			}
		}
	}
	
	//	(y, x)를 좌상단 꼭지점으로 해서 size * size인 색종이를 붙일 수 있는지 체크
	private static boolean canPaste(int y, int x, int size) {
		for(int r = y; r < y + size; r++) {
			for(int c = x; c < x + size; c++) {
				if(!isIn(r, c) || paper[r][c] == '0')
					return false;
			}
		}
		
		return true;
	}
	
	//	(y, x)를 좌상단 꼭지점으로 해서 size * size인 색종이를 붙이거나 뗌 (반대로 변경)
	private static void convert(int y, int x, int size) {
		for(int r = y; r < y + size; r++) {
			for(int c = x; c < x + size; c++)
				paper[r][c] = paper[r][c] == '0' ? '1' : '0';
		}
	}
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < 10) && (0 <= x && x < 10);
	}
} // Main-class-end
```

### BOJ 2473 세 용액

[](https://www.acmicpc.net/problem/2473)

**Binary Search (O(N^2logN))**

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
    private static long NONE = 10L * 1_000_000_000;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;                //    용액 개수
    private static long[] solutions;      //    용액
    
    private static long minSum = NONE;                          //    세 용액의 특성값 합의 절댓값의 최소
    private static final long[] minSolutions = new long[3];     //    그때 합이 0에 가장 가까운 세 용액 특성값 (오름차순)
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        st = new StringTokenizer(br.readLine());
        solutions = new long[N];
        
        for(int i = 0; i < N; i++)
            solutions[i] = Integer.parseInt(st.nextToken());
        
        Arrays.sort(solutions);    //    용액 오름차순 정렬

        for(int idx1 = 0; idx1 < N -2; idx1++) {
            for(int idx2 = idx1 + 1; idx2 < N - 1; idx2++) {
                long sum = solutions[idx1] + solutions[idx2];
                long target = -sum;    //    찾아야 할 용액 특성값
                
                //    target보다 작거나 같은 값 중 가장 큰 값    
                long lower = lowerBound(target, idx2 + 1, true);
                //    target보다 크거나 같은 값 중 가장 작은 값
                long upper = lowerBound(target, idx2 + 1, false);

                if(minSum > Math.abs(sum + lower)) {
                    minSum = Math.abs(sum + lower);
                    minSolutions[0] = solutions[idx1];                    
                    minSolutions[1] = solutions[idx2];
                    minSolutions[2] = lower;
                }
                
                if(minSum > Math.abs(sum + upper)) {
                    minSum = Math.abs(sum + upper);
                    minSolutions[0] = solutions[idx1];                    
                    minSolutions[1] = solutions[idx2];
                    minSolutions[2] = upper;
                }
            }
        }
        
        System.out.println(minSolutions[0] + " " + minSolutions[1] + " " + minSolutions[2]);
    }    //    main-end
    
    private static long lowerBound(long target, int start, boolean lower) {
        int end = N - 1;
        long res = NONE;
        
        while(start <= end) {
            int mid = (start + end) / 2;
            
            if(solutions[mid] == target)    //    target과 정확히 일치하는 값이 있을 경우
                return target;
            
            if(lower) {    //    target보다 작으면서 가장 큰 값 찾아야 함
                if(solutions[mid] < target) {    //    target보다 작을 경우
                    res = solutions[mid];    //    일단 저장
                    start = mid + 1;           //    target보다는 작으면서 더 큰 값 있나 찾기
                }
                else    //    target보다 클 경우
                    end = mid - 1;    //    target보다 작은 값 찾아야 함
            }
            else {    //    target보다 크면서 가장 작은 값 찾아야 함
                if(solutions[mid] < target)    //    target보다 작을 경우
                    start = mid + 1;           //    target보다 큰 값 찾아야 함
                else {    //    target보다 클 경우
                    res = solutions[mid];    //    일단 저장
                    end = mid - 1;            //    target보다는 크면서 더 작은 값 있나 찾기
                }
            }
        }
        
        return res;
    }
}    //    Main-class-end
```

**Two-Pointer (O(N^2))**

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
    private static long NONE = 10L * 1_000_000_000;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;                //    용액 개수
    private static long[] solutions;      //    용액
    
    private static long minSum = NONE;                          //    세 용액의 특성값 합의 절댓값의 최소
    private static final long[] minSolutions = new long[3];     //    그때 합이 0에 가장 가까운 세 용액 특성값 (오름차순)
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        st = new StringTokenizer(br.readLine());
        solutions = new long[N];
        
        for(int i = 0; i < N; i++)
            solutions[i] = Integer.parseInt(st.nextToken());
        
        Arrays.sort(solutions);    //    용액 오름차순 정렬

        for(int left = 0; left < N -2; left++) {
            int mid = left + 1;
            int right = N - 1;
            
            long target = -solutions[left];
            
            while(mid < right) {
                if(Math.abs(solutions[left] + solutions[mid] + solutions[right]) < minSum) {
                    minSum = Math.abs(solutions[left] + solutions[mid] + solutions[right]);
                    minSolutions[0] = solutions[left];
                    minSolutions[1] = solutions[mid];
                    minSolutions[2] = solutions[right];
                }
                
                if(solutions[mid] + solutions[right] < target)
                	mid++;
                else if(solutions[mid] + solutions[right] == target) {
                	System.out.println(minSolutions[0] + " " + minSolutions[1] + " " + minSolutions[2]);
                	return;
                }
                else
                	right--;
            }
        }
        
        System.out.println(minSolutions[0] + " " + minSolutions[1] + " " + minSolutions[2]);
    }    //    main-end
}    //    Main-class-end
```

### 삼성전자 2023년 상반기 오후 1번 메이즈 러너

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/training-field/frequent-problems/problems/rabit-and-race/description?page=3&pageSize=5)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int OUT = 0;	//	참가자가 미로에서 나갔을 경우, 좌표 (0, 0)으로 변경
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;	//	미로 크기
	private static int M;	//	참가자 수
	private static int K;	//	게임 시간
	
	private static int[][] maze;	//	미로
	
	private static int[][] participants;	//	participants[nth] = nth번째 참가자 위치 정보 {y, x}

	private static int remains = 0;			//	탈출하지 못한 참가자 수
	
	private static int movedDist = 0;		//	게임 끝날때까지 참가자들이 움직인 거리 합
	private static int exitY = 0;
	private static int exitX = 0;			//	출구 좌표
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		maze = new int[N + 1][N + 1];
		participants = new int[M + 1][2];
		remains = M;
		
		for(int r = 1; r <= N; r++) {
			st = new StringTokenizer(br.readLine());
			for(int c = 1; c <= N; c++)
				maze[r][c] = Integer.parseInt(st.nextToken());
		}
		
		for(int m = 1; m <= M; m++) {
			st = new StringTokenizer(br.readLine());
			participants[m][0] = Integer.parseInt(st.nextToken());
			participants[m][1] = Integer.parseInt(st.nextToken());
		}
		
		st = new StringTokenizer(br.readLine());
		exitY = Integer.parseInt(st.nextToken());
		exitX = Integer.parseInt(st.nextToken());

		for(int k = 1; k <= K; k++) {
			step1();
			if(remains == 0)
				break;
			step2();
		}
		
		System.out.println(movedDist);
		System.out.println(exitY + " " + exitX);
	}	//	main-end
	
	private static void step1() {
		for(int m = 1; m <= M; m++) {	//	m번째 참가자
			int cy = participants[m][0];
			int cx = participants[m][1];
			
			if(cy == OUT && cx == OUT)	//	이미 나간 참가자의 경우
				continue;
			
			int curDist = dist(cy, cx, exitY, exitX);	//	(cy, cx)에서 (exitY, exitX)까지의 최단거리
			int dir = -1;	//	최종적으로 움직일 방향
		
			for(int d = 0; d < 4; d++) {
				if(isIn(cy + dy[d], cx + dx[d]) && maze[cy + dy[d]][cx + dx[d]] == 0) {	//	d 방향으로 한 칸 간 칸이 미로 내부이고, 벽이 아닐 경우
					if(dist(cy + dy[d], cx + dx[d], exitY, exitX) < curDist) {	//	d 방향으로 움직였을때 더 가까워 질 경우
						dir = d;
						break;
					}
				}
			}
			
			if(dir != -1) {	//	m번째 참가자가 이동할 수 있을 경우
				participants[m][0] += dy[dir];
				participants[m][1] += dx[dir];	//	해당 방향으로 이동
				movedDist++;
				
				if(participants[m][0] == exitY && participants[m][1] == exitX) {	//	해당 플레이어가 탈출한 경우
					participants[m][0] = OUT;
					participants[m][1] = OUT;
					remains--;
				}
			}
		}
	}
	
	private static void step2() {
		rotate(selectPartialSquare());
	}
	
	//	출구와 한 명 이상의 참가자를 포함하는 최소 정사각형 고르기
	//	{luy, lux, rdy, rdx} 반환
	private static int[] selectPartialSquare() {
		int minSize = Integer.MAX_VALUE;	//	최소 정사각형 크기
		int minLuy = Integer.MAX_VALUE;
		int minLux = Integer.MAX_VALUE;	//	최소 정사각형 좌상단 좌표
		int minRdy = Integer.MAX_VALUE;
		int minRdx = Integer.MAX_VALUE;	//	최소 정사각형 우하단 좌표
		
		for(int m = 1; m <= M; m++) {
			int py = participants[m][0];
			int px = participants[m][1];	//	포함시킬 참가자 좌표
			
			if(py == OUT && px == OUT)	//	이미 탈출한 참가자일 경우
				continue;
			
			int size = Math.max(Math.abs(py - exitY), Math.abs(px - exitX)) + 1;	//	해당 플레이어를 포함시키기 위한 최소 정사각형 크기
			
			if(size > minSize)	//	이미 더 작은 정사각형 찾은 경우
				continue;
			
			int luy = 1;
			int lux = 1;
			int rdy = 0;
			int rdx = 0;
			
			OUTER:
			for(luy = 1; luy <= N - size + 1; luy++) {
				for(lux = 1; lux <= N - size + 1; lux++) {	//	정사각형 좌상단 좌표
					rdy = luy + size - 1;
					rdx = lux + size - 1;	//	정사각형 우하단 좌표
					
					//	해당 정사각형 범위 안에 참가자와 출구가 모두 포함될 경우, 해당 정사각형이 후보임
					if(isIn(py, px, luy, rdy, lux, rdx) && isIn(exitY, exitX, luy, rdy, lux, rdx))
						break OUTER;
				}
			}
			
			if(size < minSize) {	//	정사각형 크기가 더 작을 경우
				minSize = size;
				minLuy = luy;
				minLux = lux;
				minRdy = rdy;
				minRdx = rdx;
			}
			else {	//	정사각형 크기는 같을 경우
				//	좌상단 y 좌표가 더 작거나, 좌상단 y 좌표가 같을때 좌상단 x 좌표가 더 작을 경우 
				if(luy < minLuy || (luy == minLuy && lux < minLux)) {
					minLuy = luy;
					minLux = lux;
					minRdy = rdy;
					minRdx = rdx;
				}
			}
		}
		
		return new int[] {minLuy, minLux, minRdy, minRdx};
	}
	
	//	좌상단 좌표가 (luy, lux), 우하단 좌표가 (rdy, rdx)인 부분 정사각형을 시계방향으로 90도 회전함
	private static void rotate(int[] partialSquare) {
		int luy = partialSquare[0];
		int lux = partialSquare[1];
		int rdy = partialSquare[2];
		int rdx = partialSquare[3];
		
		int[][][] mapping = new int[N + 1][N + 1][2];	//	mapping[y][x] : 원래 (y, x)칸이 회전 후 위치할 좌표 {y', x'}
		
		int xx = rdx;
		
		for(int y = luy; y <= rdy; y++) {
			int yy = luy;
			for(int x = lux; x <= rdx; x++) {
				mapping[y][x][0] = yy++;
				mapping[y][x][1] = xx;
			}
			
			xx--;
		}
		
		int[][] nMaze = copyMaze();	//	기존 미로 복사
		
		boolean[] rotated = new boolean[M + 1];	//	참가자가 회전됐는지 확인
		boolean exitRotated = false;	//	출구가 회전됐는지 확인
		
		for(int cy = luy; cy <= rdy; cy++) {
			for(int cx = lux; cx <= rdx; cx++) {	//	현재 미로칸 (cy, cx)
				int ny = mapping[cy][cx][0];
				int nx = mapping[cy][cx][1];		//	현재 미로칸 (cy, cx)가 회전 결과로 이동할 칸
			
				if(maze[cy][cx] >= 1)	//	현재 미로칸이 벽일 경우
					nMaze[ny][nx] = maze[cy][cx] - 1;	//	벽 내구도 1 감소시켜서 복사
				else {
					nMaze[ny][nx] = maze[cy][cx];
					if(cy == exitY && cx == exitX && !exitRotated) {	//	현재 칸이 출구일 경우
						exitY = ny;
						exitX = nx;	//	출구 변경
						exitRotated = true;
					}
				}
				
				for(int m = 1; m <= M; m++) {	//	(cy, cx)에 있던 참가자 좌표 갱신시키기
					if(participants[m][0] == cy && participants[m][1] == cx && !rotated[m]) {
						rotated[m] = true;
						participants[m][0] = ny;
						participants[m][1] = nx;
					}
				}
			}
		}
		
		maze = nMaze;	//	새로운 미로로 갈아타기
	}
	
	private static int[][] copyMaze() {
		int[][] copied = new int[N + 1][N + 1];
		
		for(int r = 1; r <= N; r++) {
			for(int c = 1; c <= N; c++)
				copied[r][c] = maze[r][c];
		}
		
		return copied;
	}
	
	private static final int[] dy = {-1, 1, 0, 0};
	private static final int[] dx = {0, 0, -1, 1};
	
	private static boolean isIn(int y, int x) {
		return isIn(y, x, 1, N, 1, N);
	}
	
	private static boolean isIn(int y, int x, int fromY, int toY, int fromX, int toX) {
		return (fromY <= y && y <= toY) && (fromX <= x && x <= toX);
	}
	
	private static int dist(int y1, int x1, int y2, int x2) {
		return Math.abs(y1 - y2) + Math.abs(x1 - x2);
	}
}	//	Main-class-end
```