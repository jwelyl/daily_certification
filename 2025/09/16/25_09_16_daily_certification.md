# 25_09_16_daily_certification

# Problem Solving (Algorithm & SQL)

### SW Expert Academy 2112. 보호 필름

[SW Expert Academy](https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV5V1SYKAaUDFAWu)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Solution {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int T;
	private static int D; // 보호필름 두께
	private static int W; // 보호필름 가로 길이
	private static int K; // 합격 기준 K

	private static int[][] origFilm; // 원본 보호필름 (복구용)
	private static int[][] copied; // 약물 투여에 쓰일 복제본
	private static boolean find = false; // 약물 투여해서 합격 기준 만족시킨 경우 true

	public static void main(String[] args) throws IOException {
		T = Integer.parseInt(br.readLine());

		for (int tc = 1; tc <= T; tc++) {
			st = new StringTokenizer(br.readLine());
			D = Integer.parseInt(st.nextToken());
			W = Integer.parseInt(st.nextToken());
			K = Integer.parseInt(st.nextToken());

			origFilm = new int[D][W];

			for (int y = 0; y < D; y++) {
				st = new StringTokenizer(br.readLine());
				for (int x = 0; x < W; x++)
					origFilm[y][x] = Integer.parseInt(st.nextToken());
			}

			copied = copy(origFilm);

			sb.append("#").append(tc).append(" ");

			if (K == 1 || pass(origFilm)) // K = 1일 경우 당연히 통과, 그렇지 않더라도 이미 합격 기준 충족한다면 통과
				sb.append(0).append("\n");
			else {
				find = false;

				for (int cnt = 1; cnt <= D; cnt++) { // 약물 투여 횟수
					backtracking(0, 0, cnt);

					if (find) { // cnt번 약물 투여했더니 합격 기준 충족
						sb.append(cnt).append("\n");
						break;
					}
				}
			}
		}

		System.out.print(sb);
	} // main-end

	private static void backtracking(int nth, int start, int targetCnt) {
		if (find) // 이미 합격 기준 만족한 경우를 찾음
			return;

		if (nth == targetCnt) { // targetCnt만큼 약물을 투여했을 경우
			if (pass(copied)) // 합격 기준을 만족했을 경우
				find = true;

			return;
		}

		for (int i = start; i < D; i++) {
			// i행에 A 약품 놓기
			put(i, 0);
			// 다음 약품 놓을 행 찾기
			backtracking(nth + 1, i + 1, targetCnt);
			// i행에 B 약품 놓기
			put(i, 1);
			// 다음 약품 놓을 행 찾기
			backtracking(nth + 1, i + 1, targetCnt);
			// i행 원상복구
			recover(i);
		}
	}

	private static void put(int row, int cell) {
		for (int x = 0; x < W; x++)
			copied[row][x] = cell;
	}

	private static void recover(int row) {
		for (int x = 0; x < W; x++)
			copied[row][x] = origFilm[row][x];
	}

	// 약물 투여용 보호필름 복사
	private static int[][] copy(int[][] film) {
		int[][] res = new int[D][W];

		for (int y = 0; y < D; y++) {
			for (int x = 0; x < W; x++)
				res[y][x] = film[y][x];
		}

		return res;
	}

	// 보호필름이 합격 기준을 만족할 경우 true 반환
	private static boolean pass(int[][] film) {
		for (int w = 0; w < W; w++) { // w열 검사
			int cur = film[0][w];
			int end = 1;
			int cnt = 1;

			boolean colPass = false;

			while (end < D) {
				if (cur == film[end][w]) {
					cnt++;

					if (cnt == K) {
						colPass = true;
						break;
					}
				} else {
					cur = film[end][w];
					cnt = 1;
				}

				end++;
			}

			if (!colPass)
				return false;
		}

		return true;
	}
} // Solution-class-end
```

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
 
public class Solution {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
     
    private static int T;
    private static int D; 
    private static int W;
    private static int K;
    private static int[][] orginFilm;
    private static int[][] copyFilm;
    private static int answer;
     
    public static void main(String[] args) throws IOException {
        T = Integer.parseInt(br.readLine());
         
        for(int tc = 1; tc <= T; tc++) {
        	st = new StringTokenizer(br.readLine());
             
            D = Integer.parseInt(st.nextToken());
            W = Integer.parseInt(st.nextToken());
            K = Integer.parseInt(st.nextToken());
         
            answer = D;
             
            orginFilm = new int[D][W];
            copyFilm = new int[D][W];
             
            for(int i = 0; i < D; i++) {
            	st = new StringTokenizer(br.readLine());
                for(int j = 0; j < W; j++)
                    orginFilm[i][j] = Integer.parseInt(st.nextToken());
            }
             
            copy(copyFilm);
             
            if(K == 1)  //  무조건 통과하는 경우
                answer = 0;
            if(isOk())  //  현재 필름으로 괜찮을 경우
                answer = 0;
            else
                dfs(0, 0);
             
            sb.append(String.format("#%d %d\n", tc, answer));
        }
         
        System.out.print(sb);
    }	//	main-end
     
    //  현재 필름이 통과일 경우 true
    private static boolean isOk() {
        for(int col = 0; col < W; col++) {   //  각 열마다 같은 셀이 K개 이상인지 검사
            int start = copyFilm[0][col];
            int cnt = 1;
             
            for(int i = 1; i < D; i++) {
                if(copyFilm[i][col] == start) {
                    cnt++;
                    if(cnt >= K)
                        break;
                }
                else {
                    start = copyFilm[i][col];
                    cnt = 1;
                }
            }
             
            if(cnt < K)    //  현재 열에 연달아 있는 같은 셀이 K개 미만일 경우 
            	return false;
        }
         
        return true;
    }
     
    //  row 행의 셀들을 to로 바꿈
    private static void changeOneLine(int row, int to) {
        for(int col = 0; col < W; col++)
            copyFilm[row][col] = to;
    }
     
    //  row 행의 셀들을 원래대로 바꿈
    private static void recover(int row) {
        for(int col = 0; col < W; col++)
            copyFilm[row][col] = orginFilm[row][col];
    }
     
    private static void copy(int[][] curFilm) {
        for(int i = 0; i < D; i++)
            for(int j = 0; j < W; j++)
                curFilm[i][j] = orginFilm[i][j];
    }
     
    //  idx번째 행 전까지 cnt개째 바꿨을 때
    private static void dfs(int nth, int cnt) {
        if(cnt >= answer)   //  이미 정답으로 구해진 개수보다 더 바꿔야하면 그만둠
            return;
         
        if(nth == D) {  //  D개의 행을 모두 처리했을 경우
            if(isOk()) 
                answer = Math.min(answer, cnt);
            return;
        }
         
        //  현재 행은 바꾸지 않고 다음 행으로 감
        dfs(nth + 1, cnt);
         
        //  현재 행을 A로 바꿈
        changeOneLine(nth, 0);
        dfs(nth + 1, cnt + 1);
         
        //  현재 행을 B로 바꿈
        changeOneLine(nth, 1);
        dfs(nth + 1, cnt + 1);
         
        //  현재 행을 원본과 같이 바꿈
        recover(nth);
    }
}	//	Solution-class-end
```

### BOJ 34079 **좋아하는 다이아몬드가 안경을 깜빡했다**

[34079번: 좋아하는 다이아몬드가 안경을 깜빡했다](http://boj.ma/34079/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.Queue;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    장소 개수
    private static int M;    //    양방향 도로 개수
    
    private static List<Integer>[] graph;
    
    private static int[] dist;        //    dist[v] : N - 1에서 v까지의 최단거리
    private static int answer = 1;    //    N에서 1로 가는 최단경로들이 반드시 공통으로 지나는 장소
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[N + 1];
        dist = new int[N + 1];
        
        for(int v = 0; v <= N; v++) {
            graph[v] = new ArrayList<>();
            dist[v] = N + 1;
        }
        
        for(int e = 0; e < M; e++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            graph[v1].add(v2);
            graph[v2].add(v1);
        }
        
        bfs1();    //    N으로부터 각 정점까지 최단거리 구하기
        bfs2();    //    1에서부터 역추적
        
        System.out.println(answer);
    }    //    main-end
    
    private static void bfs1() {
        Queue<int[]> queue = new LinkedList<>();
        dist[N] = 0;
        queue.offer(new int[] {N, dist[N]});
        
        while(!queue.isEmpty()) {
            int[] cur = queue.poll();
            int cv = cur[0];
            int cc = cur[1];
            
            for(int nv : graph[cv]) {
                if(dist[nv] == N + 1) {
                    dist[nv] = cc + 1;
                    queue.offer(new int[] {nv, dist[nv]});
                }
            }
        }
    }
    
    private static void bfs2() {
        Queue<int[]> queue = new LinkedList<>();
        
        //    intervals[d] : N에서 1까지 최단경로 중에서 N으로부터 거리가 d인 정점 중 하나, 유일할 경우 정답
        int[] intervals = new int[dist[1] + 1];
        //    cnts[d] : N에서 1까지 최단경로 중에서 N으로부터 거리가 d인 정점의 개수
        int[] cnts = new int[dist[1] + 1];
        boolean[] visited = new boolean[N + 1];
        
        queue.offer(new int[] {1, 0});
        
        while(!queue.isEmpty()) {
            int[] cur = queue.poll();
            int cv = cur[0];
            int cc = cur[1];    //    N에서 cv까지 거리
            
            for(int nv : graph[cv]) {
                if(dist[nv] == dist[1] - cc - 1 && !visited[nv]) {
                	visited[nv] = true;
                    queue.offer(new int[] {nv, cc + 1});
                    intervals[dist[nv]] = nv;
                    
                    cnts[dist[nv]]++;
                }
            }
        }
        
        for(int d = 1; d < dist[1]; d++) {
            if(cnts[d] == 1)
                answer = intervals[d];
        }
    }
}    //    Main-class-end
```