# 25_08_05_daily_certification

# Problem Solving (Algorithm & SQL)

### **Codetree 민트 초코 우유**

[Codetree | Learning to Code with Confidence](https://www.codetree.ai/ko/frequent-problems/samsung-sw/problems/mint-choco-milk/description)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.PriorityQueue;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;	//	격자 크기
	private static int T;   //	시간
	
	//  세 자리 이진수 형태
	//	001 : 민트, 010 : 초코, 100 : 우유
	//	011 : 민트초코,  101 : 민트우유, 110 : 초코우유
	//	111 : 민트초코우유
	private static int[][] favorites;
	//	신앙심 정도
	private static int[][] beliefs;
	
	//	ownerPqList[len] : 좋아하는 음식의 길이가 len인 그룹의 대표 PQ
	//	ownerPqList[1] : 민트, 초코, 우유를 좋아하는 그룹의 대표 PQ
	//	ownerPqList[2] : 민트초코, 민트우유, 초코우유를 좋아하는 그룹의 대표 PQ
	//	ownerPqList[3] : 민트초코우유를 좋아하는 그룹의 대표 PQ
	private static final PriorityQueue<Owner>[] ownerPqList = new PriorityQueue[4];
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		T = Integer.parseInt(st.nextToken());
		
		favorites = new int[N][N];
		beliefs = new int[N][N];
		
		for(int y = 0; y < N; y++) {
			String input = br.readLine();
			for(int x = 0; x < N; x++) {
				char f = input.charAt(x);
				
				if(f == 'T')
					favorites[y][x] = 1;
				else if(f == 'C')
					favorites[y][x] = 2;
				else
					favorites[y][x] = 4;
			}
		}
		
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < N; x++)
				beliefs[y][x] = Integer.parseInt(st.nextToken());
		}
		
		for(int time = 1; time <= T; time++) {
			morning();
			afternoon();
			evening();
			result();
		}
		
		System.out.print(sb);
	}	//	main-end
	
	//	아침, N^2명 학생의 신앙심 모두 1 증가
	private static void morning() {
		for(int y = 0; y < N; y++) {
			for(int x = 0; x < N; x++)
				beliefs[y][x]++;
		}
	}
	
	private static void afternoon() {
		for(int i = 1; i <= 3; i++)
			ownerPqList[i] = new PriorityQueue<>();
		
		boolean[][] visited = new boolean[N][N];
		
		for(int y = 0; y < N; y++) {
			for(int x = 0; x < N; x++) {
				if(!visited[y][x]) {
					//	(y, x)가 속한 그룹의 대표 정보
					int[] ownerInfo = bfs(y, x, visited);
					int oy = ownerInfo[0];
					int ox = ownerInfo[1];	//	대표의 좌표
					int of = favorites[oy][ox];
					
					if(of == 1 || of == 2 || of == 4)
						ownerPqList[1].offer(new Owner(oy, ox, of, beliefs[oy][ox]));
					else if(of == 3 || of == 5 || of == 6)
						ownerPqList[2].offer(new Owner(oy, ox, of, beliefs[oy][ox]));
					else
						ownerPqList[3].offer(new Owner(oy, ox, of, beliefs[oy][ox]));
				}			
			}
		}
	}
	
	private static void evening() {
		boolean[][] propagated = new boolean[N][N];
		
		for(int i = 1; i <= 3; i++) {
			while(!ownerPqList[i].isEmpty()) {
				Owner owner = ownerPqList[i].poll();
				int oy = owner.y;
				int ox = owner.x;
				int of = owner.f;
				int ob = owner.b;
				int x = ob - 1;
				
				if(propagated[oy][ox])	//	이미 다른 전파자에게 전파를 받았으면
					continue;	//	전파를 하지 않음
				
				beliefs[oy][ox] = 1;	//	전파자 신앙심 1만 남김
				
				int dir = ob % 4;
				
				int cy = oy;
				int cx = ox;
				
				while(isIn(cy, cx) && x > 0) {
					if(favorites[cy][cx] != of) {	//	다른 음식 좋아하는 학생을 만났을 경우, 전파 시도
						int y = beliefs[cy][cx];
						
						if(x > y) {	//	강한 전파 성공
							favorites[cy][cx] = of;	//	전파자의 음식만 좋아하게 됨
							beliefs[cy][cx]++;
							x -= (y + 1);
						}
						else {	//	약한 전파 성공
							favorites[cy][cx] |= of;	//	전파자의 음식도 좋아하게 됨
							beliefs[cy][cx] += x;
							x = 0;
						}
						
						propagated[cy][cx] = true;	//	(cy, cx)는 전파를 받았으므로 전파자가 될 수 없음
					}
					
					cy += dy[dir];
					cx += dx[dir];
				}
			}
		}
	}
	
	// 	최종 결산, 각 음식 별 신앙심 집계
	private static void result() {
		int tcm = 0;	//	민트초코우유 (111)
		int tc = 0;		//	민트초코 (011)
		int tm = 0;		//	민트우유 (101)
		int cm = 0;		//	초코우유 (110)
		int m = 0;		//	우유 (100)
		int c = 0;		//	초코 (010)
		int t = 0;		//	민트 (001)
		
		for(int y = 0; y < N; y++) {
			for(int x = 0; x < N; x++) {
				int b = beliefs[y][x];
				int f = favorites[y][x];
				
				if(f == 1)
					t += b;
				else if(f == 2)
					c += b;
				else if(f == 4)
					m += b;
				else if(f == 6)
					cm += b;
				else if(f == 5)
					tm += b;
				else if(f == 3)
					tc += b;
				else
					tcm += b;
			}
		}
		
		sb.append(tcm).append(" ").append(tc).append(" ").append(tm).append(" ")
		.append(cm).append(" ").append(m).append(" ").append(c).append(" ").append(t).append("\n");
	}
	

	private static int[] bfs(int y, int x, boolean[][] visited) {
		Queue<int[]> queue = new LinkedList<>();
		List<int[]> groupPos = new ArrayList<>();	//	그룹에 속한 좌표들
		
		int resB = beliefs[y][x];	//	대표의 신앙심
		int resY = y;
		int resX = x;				//	대표의 좌표
		
		visited[y][x] = true;
		queue.offer(new int[] {y, x});
		groupPos.add(new int[] {y, x});
		
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				
				if(isIn(ny, nx) && favorites[ny][nx] == favorites[y][x] && !visited[ny][nx]) {
					visited[ny][nx] = true;
					queue.offer(new int[] {ny, nx});
					groupPos.add(new int[] {ny, nx});
					
					if(beliefs[ny][nx] > resB) {
						resB = beliefs[ny][nx];
						resY = ny;
						resX = nx;
					}
					else if(beliefs[ny][nx] == resB) {
						if(ny < resY) {
							resY = ny;
							resX = nx;
						}
						else if(ny == resY) {
							if(nx < resX)
								resX = nx;
						}
					}
				}
			}
		}
		
		for(int[] pos : groupPos) {
			int py = pos[0];
			int px = pos[1];
			
			if(py == resY && px == resX)
				beliefs[py][px] += (groupPos.size() - 1);
			else
				beliefs[py][px]--;
		}
		
		return new int[] {resY, resX};
	}
	
	private static final int[] dy = {-1, 1, 0, 0};
	private static final int[] dx = {0, 0, -1, 1};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
	
	private static class Owner implements Comparable<Owner> {
		int y;
		int x;
		int f;
		int b;
		
		public Owner(int y, int x, int f, int b) {
			this.y = y;
			this.x = x;
			this.f = f;
			this.b = b;
		}
		
		@Override
		public int compareTo(Owner other) {
			//	대표자의 신앙심이 높을 수록 우선순위 높음
			int res = Integer.compare(other.b, this.b);
			
			if(res == 0)	//	대표자의 신앙심이 같을 경우
				res = Integer.compare(this.y, other.y);	//	대표자의 y좌표가 작을 수록 우선순위가 높음
			if(res == 0)	//	대표자의 y좌표도 같을 경우
				res = Integer.compare(this.x, other.x);	//	대표자의 x좌표가 작을 수록 우선순위가 높음
			
			return res;
		}
	}
}	//	Main-class-end

```

### BOJ 17179 케이크 자르기

[17179번: 케이크 자르기](http://boj.ma/17179/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;    //    롤케이크 자르는 횟수 Q를 결정하는 횟수
    private static int M;    //    롤케이크 자를 수 있는 지점 개수
    private static int L;    //    롤케이크 길이
    
    private static int[] cuts;    //    롤케이크 자를 위치
    private static int Q;    //    하나의 롤케이크를 자르는 횟수
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        L = Integer.parseInt(st.nextToken());
        
        cuts = new int[M];
        for(int i = 0; i < M; i++)
            cuts[i] = Integer.parseInt(br.readLine());
        
        for(int i = 0; i < N; i++) {
            Q = Integer.parseInt(br.readLine());
            
            sb.append(parametricSearch()).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
    
    //    Q번 잘라서 생기는 Q + 1 조각 중 가장 작은 조각 길이의 최댓값
    private static int parametricSearch() {
        int start = 1;
        int end = L;
        int res = 1;
        
        while(start <= end) {
            int mid = start + (end - start) / 2;    //    아무리 작은 조각도 mid 이상은 되어야 한다고 가정
            int cnt = 0;    //    자르는 횟수
            int len = 0;    //    잘려진 조각 누적합
            
            boolean ok = false;
            
            for(int i = 0; i < M; i++) {
                if(cuts[i] - len >= mid) {    //    i번째 위치에서 잘랐을때 생기는 조각이 mid 이상일 경우
                    cnt++;    //    자르는 횟수 1 증가
                    len = cuts[i];
                    
                    if(cnt == Q) {    //    Q번 잘랐을 때
                        if(L - cuts[i] >= mid)    //    마지막 남은 조각 길이도 mid 이상일 경우
                            ok = true;
                        
                        break;
                    }
                }
            }
            
            if(ok) {    //    mid 이상의 크기가 되도록 잘랐을때 Q번 자를 수 있을 경우
                res = mid;
                start = mid + 1;    //    조각의 최소 길이를 더 키워보기
            }
            else        //    mid 이상의 크기가 되도록 Q번 자를 수 없을 경우
                end = mid - 1;      //    조각의 최소 길이를 더 작게 해보기
        }
        
        return res;
    }
}    //    Main-class-end
```