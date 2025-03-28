# 25_03_29_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 24305 **ОТЧЕТ**

[24305번: ОТЧЕТ](http://boj.ma/24305/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Stack;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static final Stack<int[]> stack = new Stack<>();
    private static int N;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++) {
            int cnum = Integer.parseInt(st.nextToken());
            int day = 0;
            
            while(!stack.isEmpty()) {
                int pnum = stack.peek()[0];    //    이전 수
                int pday = stack.peek()[1];    //    이전 수가 있던 날짜
                
                if(pnum < cnum) {
                    day = pday;
                    break;
                }
                
                stack.pop();
            }
            
            sb.append(day).append(" ");
            
            stack.push(new int[] {cnum, i + 1});
        }
        
        System.out.println(sb);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 7569 토마토

[7569번: 토마토](http://boj.ma/7569/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int E = -1;
	private static final int RT = 1; // 각각 빈칸, 익은 토마토

	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static final Queue<RipenTomato> queue = new LinkedList<>();	//	bfs에 사용할 Queue, 처음 입력받을 때 익은 토마토들을 저장할 목적으로도 사용

	private static int[][][] graph;	//	토마토 저장 상태(편의상 graph라 함)
	private static int M;
	private static int N;
	private static int H;	//	가로, 세로, 높이

	private static int nTomatoes = 0;
	private static int nRTomatoes = 0;
	private static int nNRTomatoes = 0; // 각각 전체 토마토, 익은 토마토, 안익은 토마토 개수
	private static int days = 0;	//	모두 익을 때까지 소모 일수

	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		M = Integer.parseInt(st.nextToken());
		N = Integer.parseInt(st.nextToken());
		H = Integer.parseInt(st.nextToken());

		graph = new int[H][N][M];

		for (int h = 0; h < H; h++) {
			for (int n = 0; n < N; n++) {
				st = new StringTokenizer(br.readLine());
				for (int m = 0; m < M; m++) {
					graph[h][n][m] = Integer.parseInt(st.nextToken());

					if (graph[h][n][m] != E) {	//	빈 칸이 아닐 경우
						nTomatoes++;
						
						if (graph[h][n][m] == RT) { // 익은 토마토의 경우
							nRTomatoes++;
							queue.offer(new RipenTomato(h, n, m));
						}
						else	// 안익은 토마토의 경우
							nNRTomatoes++;
					}
				}
			}
		}
		
		if(nTomatoes == nRTomatoes)	//	처음 입력받았을 때 익은 토마토 수랑 전체 토마토 수가 같을 경우, 즉 처음부터 모두 익은 상태인 경우
			System.out.println(0);
		else {
			bfs();	// bfs로 익은 토마토들을 중심으로 안익은 토마토들도 익혀나감
			System.out.println(days);
		}
	}	//	main-end
	
	private static final int[] dz = { 0, 0, 0, 0, -1, 1 };
	private static final int[] dy = { 0, 1, 0, -1, 0, 0 };
	private static final int[] dx = { 1, 0, -1, 0, 0, 0 };	//	6방향
	
	private static boolean isIn(int z, int y, int x) {
		return (0 <= z && z < H) && (0 <= y && y < N) && (0 <= x && x < M);
	}
	
	private static void bfs() {
		int input = queue.size();	//	그날 새로 익은 토마토 개수(해당 토마토들 기준으로 주변에 안익은 토마토들이 익어감)
		
		while(!queue.isEmpty()) {
			int tmp = 0;
			for(int i = 0; i < input; i++) {	//	오늘 새로 익은 토마토들
				RipenTomato cur = queue.poll();
				int cz = cur.z;
				int cy = cur.y;
				int cx = cur.x;
				
				for(int d = 0; d < 6; d++) {	//	그 중 한 토마토의 주변 6방향 확인
					int nz = cz + dz[d];
					int ny = cy + dy[d];
					int nx = cx + dx[d];
				
					//	다음 칸이 범위를 벗어날 경우
					if(!isIn(nz, ny, nx))
						continue;
					
					//	다음 칸이 이미 익은 토마토이거나 빈 칸일 경우
					if(graph[nz][ny][nx] == E || graph[nz][ny][nx] == RT)
						continue;
				
					nNRTomatoes -= 1;	//	덜 익은 토마토 1개 감소
					nRTomatoes += 1;	//	익은 토마토 1개 증가
					
					if(nRTomatoes == nTomatoes && nNRTomatoes == 0) {	//	모든 토마토가 익었을 경우
						days++;	//	그 날 하루 추가
						return;
					}
					
					tmp++;				//	오늘 하루 새로 익은 토마토 1개 증가
					graph[nz][ny][nx] = RT;
					queue.offer(new RipenTomato(nz, ny, nx));
				}	//	익은 토마토 기준 6방향 확인
			}	//	하루에 새로 익을 토마토 구하기
			
			input = tmp;
			days++;
		}	//	while-end
		days = -1;	//	모든 토마토가 익지 못할 경우
	}
	
	private static class RipenTomato {
		public int z;
		public int y;
		public int x;

		public RipenTomato(int z, int y, int x) {
			this.z = z;
			this.y = y;
			this.x = x;
		}
	}
}	//	Main-class-end
```