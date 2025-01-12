# 25_01_12_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ** 15684 사다리 조작

[15684번: 사다리 조작](https://boj.ma/15684/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;	//	세로선 개수
	private static int M;	//	주어진 가로선 개수
	private static int H;	//	세로선에 가로선을 높을 수 있는 위치 개수
	
	//	ladder[h][c] : 세로선 c와 세로선 c + 1 사이에 위치 h의 가로선이 놓여있음
	private static boolean[][] ladder;
	
	private static int answer = -1;	//	세로선 i(1 <= i <= N)에서 시작했을때 세로선 i에서 끝나기 위해 놓아야 할 가로선 최소 개수
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		H = Integer.parseInt(st.nextToken());
		
		if(M == 0) {	//	주어진 가로선이 없을 경우 세로선 i에서 시작하면 무조건 세로선 i에서 끝남
			System.out.println(0);
			return;
		}
		
		ladder = new boolean[H + 1][N];
		for(int i = 0; i < M; i++) {
			st = new StringTokenizer(br.readLine());
			int a = Integer.parseInt(st.nextToken());
			int b = Integer.parseInt(st.nextToken());
			
			ladder[a][b] = true;	//	세로선 b와 세로선 b + 1을 위치 a인 가로선으로 연결
		}
		
		//	3개 이하의 가로선을 추가해보기
		for(int add = 0; add <= 3; add++) {
			backtracking(0, 1, add);
			if(answer != -1)
				break;
		}
		
		System.out.println(answer);
	}	//	main-end
	
	private static boolean simulation() {
		for(int col = 1; col <= N; col++) {	//	col번째 세로선에서 start
			int crow = 1;
			int ccol = col;
			
			while(crow <= H) {
				if(ccol >= 2 && ladder[crow][ccol - 1])	//	ccol - 1번째 세로선과 ccol번째 세로선이 crow에서 가로선으로 연결됨
					ccol--;	//	왼쪽으로 이동
				else if(ccol < N && ladder[crow][ccol])	//	ccol번째 세로선과 ccol + 1번째 세로선이 crow에서 가로선으로 연결됨
					ccol++;	//	오른쪽으로 이동
				
				crow++;	//	한칸 내려감
			}
			
			if(ccol != col)	//	col번째 세로선에서 시작했는데 col번째 세로선에서 끝나지 않은 경우
				return false;
		}
		
		return true;
	}
	
	private static void backtracking(int nth, int start, int add) {
		if(answer != -1)	//	이미 최소 가로선 개수를 찾은 경우, 더는 안해도 된다.
			return;
		
		if(nth == add) {		//	add개의 가로선을 무사히 추가한 경우
			if(simulation())	//	추가한 가로선으로 사다리 게임 한 결과 i번째 세로선에서 시작했을때 i번째 세로선에서 끝나면	
				answer = add;	//	최소한 add개의 세로선을 추가하면 된다.
			
			return;
		}
		
		for(int num = start; num <= (N - 1) * H; num++) {
			int y = num % (N - 1) == 0 ? num / (N - 1) : num / (N - 1) + 1;
			int x = num % (N - 1) == 0 ? N - 1 : num % (N - 1);
			
			//	이미 위치 y에서 x, x + 1 세로선이 가로선으로 연결되어 있거나
			//	x - 1, x 세로선이 가로선으로 연결되어 있거나
			//	x, x + 1 세로선이 가로선으로 연결되어 있으면
			if(ladder[y][x] || (x - 1 >= 1 && ladder[y][x - 1]) || (x + 1 < N && ladder[y][x + 1]))
				continue;
			
			ladder[y][x] = true;	//	위치 y에서 x, x + 1 세로선을 가로선으로 연결
			backtracking(nth + 1, num + 1, add);
			ladder[y][x] = false;	//	위치 y에서 x, x + 1 세로선을 가로선으로 연결 해제
		}
	}
}	//	Main-class-end
```

### **BOJ 20061 모노미노도미노 2**

[20061번: 모노미노도미노 2](https://boj.ma/20061/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static final boolean[][] blues = new boolean[4][6];
	private static final boolean[][] greens = new boolean[6][4];
	
	private static int N;	//	블록 놓은 횟수
	
	private static int answer = 0;	//	얻은 점수
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		for(int i = 0; i < N; i++) {
			st = new StringTokenizer(br.readLine());
			int type = Integer.parseInt(st.nextToken());
			int x = Integer.parseInt(st.nextToken());
			int y = Integer.parseInt(st.nextToken());
			
			put(type, x, y);
			deleteFullLines();
			lightColors();
		}
		
		System.out.println(answer);
		System.out.println(cnts());
	}	//	main-end
	
	//	type 블록을 (x, y)에 놓아서 blues 영역과 greens 영역으로 이동시킴
	private static void put(int type, int x, int y) {
		putBlue(type, x, y);
		putGreen(type, x, y);
	}
	
	private static void putBlue(int type, int x, int y) {
		int cy = 0;
		
		if(type == 1) {
			while(true) {
				if(cy == 5 || blues[x][cy + 1])		//	더 이상 갈수 없거나 다음에 가려는 칸이 이미 블록이 있을 경우
					break;
				cy++;
			}
			
			blues[x][cy] = true;
		}
		else if(type == 2) {
			while(true) {
				if(cy == 4 || blues[x][cy + 2])		//	더 이상 갈수 없거나 다음에 가려는 칸이 이미 블록이 있을 경우
					break;
				cy++;
			}
			
			blues[x][cy] = true;
			blues[x][cy + 1] = true;
		}
		else {
			while(true) {
				if(cy == 5 || blues[x][cy + 1] || blues[x + 1][cy + 1])	//	더 이상 갈수 없거나 다음에 가려는 칸이 이미 블록이 있을 경우
					break;
				cy++;
			}
			
			blues[x][cy] = true;
			blues[x + 1][cy] = true;
		}
	}
	
	private static void putGreen(int type, int x, int y) {
		int cx = 0;
		
		if(type == 1) {
			while(true) {
				if(cx == 5 || greens[cx + 1][y])		//	더 이상 갈수 없거나 다음에 가려는 칸이 이미 블록이 있을 경우
					break;
				cx++;
			}
			
			greens[cx][y] = true;
		}
		else if(type == 2) {
			while(true) {
				if(cx == 5 || greens[cx + 1][y] || greens[cx + 1][y + 1])	//	더 이상 갈수 없거나 다음에 가려는 칸이 이미 블록이 있을 경우
					break;
				cx++;
			}
			
			greens[cx][y] = true;
			greens[cx][y + 1] = true;
		}
		else {			
			while(true) {
				if(cx == 4 || greens[cx + 2][y])		//	더 이상 갈수 없거나 다음에 가려는 칸이 이미 블록이 있을 경우
					break;
				cx++;
			}
			
			greens[cx][y] = true;
			greens[cx + 1][y] = true;
		}
	}
	
	//	blues/greens 영역을 확인해서 가득 찬 열/행이 있으면 그 열/행 수만큼 점수를 얻고 열/행을 제거한 뒤에 열/행 이동시킴
	private static void deleteFullLines() {
		//	blues 영역 확인
		for(int y = 2; y < 6; y++) {
			int cnt = 0;
			
			for(int x = 0; x < 4; x++) {
				if(blues[x][y])
					cnt++;
			}
			
			if(cnt == 4) {	//	y 열이 가득찼을 경우
				answer++;
				deleteBlueLine(y);
			}
		}
		
		//	greens 영역 확인
		for(int x = 2; x < 6; x++) {
			int cnt = 0;
			
			for(int y = 0; y < 4; y++) {
				if(greens[x][y])
					cnt++;
			}
			
			if(cnt == 4) {	//	x 행이 가득찼을 경우
				answer++;
				deleteGreenLine(x);
			}
		}
	}
	
	//	y열을 지우고 0 ~ y - 1 열까지 한 열 이동
	private static void deleteBlueLine(int y) {
		for(int cy = y; cy >= 1; cy--) {
			for(int x = 0; x < 4; x++) {
				blues[x][cy] = blues[x][cy - 1];
				blues[x][cy - 1] = false;
			}
		}
	}
	
	//	x행을 지우고 0 ~ x - 1 행까지 한 행 이동
	private static void deleteGreenLine(int x) {
		for(int cx = x; cx >= 1; cx--) {
			for(int y = 0; y < 4; y++) {
				greens[cx][y] = greens[cx - 1][y];
				greens[cx - 1][y] = false;
			}
		}
	}
	
	//	blues/greens 영역의 연한 칸 영역에 블록이 남아있을 경우 블록이 남은 연한 칸 열/행 개수만큼 블록들을 열/행 이동시킴 
	private static void lightColors() {
		int deleteLines = 0;
		
		//	blues 연한 영역 확인
		for(int y = 0; y < 2; y++) {	//	0, 1 열 확인
			for(int x = 0; x < 4; x++) {
				if(blues[x][y]) {	//	y열에 블록이 하나라도 있으면
					deleteLines++;
					break;
				}
			}
		}
		
		if(deleteLines >= 1)
			deleteBlueLine(5);
		if(deleteLines == 2)
			deleteBlueLine(5);
		
		deleteLines = 0;
		
		//	greens 연한 영역 확인
		for(int x = 0; x < 2; x++) {	//	0, 1 행 확인
			for(int y = 0; y < 4; y++) {
				if(greens[x][y]) {	//	x행에 블록이 하나라도 있으면
					deleteLines++;
					break;
				}
			}
		}
		
		if(deleteLines >= 1)
			deleteGreenLine(5);
		if(deleteLines == 2)
			deleteGreenLine(5);
	}
	
	private static int cnts() {
		int res = 0;
		
		for(int x = 0; x < 4; x++) {
			for(int y = 0; y < 6; y++) {
				if(blues[x][y])
					res++;
				if(greens[y][x])
					res++;
			}
		}
		
		return res;
	}
}	//	Main-class-end
```

### **BOJ 10217 KCM Travel**

[10217번: KCM Travel](https://boj.ma/10217/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.PriorityQueue;

public class Main {
    private static final int INF = 10_000 * 1_000 + 1;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int T;    //    테스트케이스 개수
    private static int N;    //    공항 개수
    private static int M;    //    지원 비용
    private static int K;    //    항공편 개수
    
    private static List<int[]>[] graph;
    
    public static void main(String[] args) throws IOException {
        T = Integer.parseInt(br.readLine());
        
        for(int tc = 1; tc <= T; tc++) {
            st = new StringTokenizer(br.readLine());
            N = Integer.parseInt(st.nextToken());
            M = Integer.parseInt(st.nextToken());
            K = Integer.parseInt(st.nextToken());
            
            graph = new ArrayList[N + 1];
            for(int v = 1; v <= N; v++)
                graph[v] = new ArrayList<>();
            
            for(int e = 0; e < K; e++) {
                st = new StringTokenizer(br.readLine());
                int v1 = Integer.parseInt(st.nextToken());
                int v2 = Integer.parseInt(st.nextToken());
                int c = Integer.parseInt(st.nextToken());
                int d = Integer.parseInt(st.nextToken());
                
                graph[v1].add(new int[] {v2, c, d});
            }
            
            for(int v = 1; v <= N; v++)
            	Collections.sort(graph[v], (e1, e2) -> Integer.compare(e1[2], e2[2]));
        
            sb.append(dijkstra()).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static String dijkstra() {
        PriorityQueue<int[]> pq = new PriorityQueue<>((n1, n2) -> Integer.compare(n1[2], n2[2]));
        int[][] dist = new int[M + 1][N + 1];
        for(int c = 0; c <= M; c++)
            Arrays.fill(dist[c], INF);
        
        dist[0][1] = 0;
        pq.offer(new int[] {1, 0, dist[0][1]});
        
        while(!pq.isEmpty()) {
            int[] cur = pq.poll();
            int cv = cur[0];
            int cc = cur[1];
            int cd = cur[2];
            
            if(cv == N)
            	break;
            
            if(dist[cc][cv] < cd)
                continue;
            
            for(int[] edge : graph[cv]) {
                int nv = edge[0];
                int nc = cc + edge[1];
                int nd = cd + edge[2];
                
                if(nc <= M && nd < dist[nc][nv]) {
                	for(int nnc = nc + 1; nnc <= M; nnc++) {
                		if(dist[nnc][nv] <= nd)
                			break;
                		dist[nnc][nv] = nd;
                	}
                	
                	dist[nc][nv] = nd;
                	pq.offer(new int[] {nv, nc, nd});
                }
            }
        }
        
        int res = INF;
        for(int c = 0; c <= M; c++) {
            if(dist[c][N] < res)
                res = dist[c][N];
        }
        
        return res == INF ? "Poor KCM" : Integer.toString(res);
    }
}    //    Main-class-end
```