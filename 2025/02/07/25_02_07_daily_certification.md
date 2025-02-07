# 25_02_07_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 22345 누적 거리

[22345번: 누적 거리](http://boj.ma/22345/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Collections;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;    //    마을 개수
    private static int Q;    //    질의 개수
    
    private static final List<Town> towns = new ArrayList<>();
    
    private static long[] prefixSumA;    //    prefixSumA[i] : 0 ~ i 마을의 a 누적합
    private static long[] prefixSumAX;   //    prefixSumAX[i] : 0 ~ i 마을의 a * x 누적합
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        Q = Integer.parseInt(st.nextToken());
        
        for(int t = 0; t < N; t++) {
            st = new StringTokenizer(br.readLine());
            int a = Integer.parseInt(st.nextToken());
            int x = Integer.parseInt(st.nextToken());
            
            towns.add(new Town(a, x));
        }
        
        Collections.sort(towns);
        
        prefixSumA = new long[N];
        prefixSumAX = new long[N];
        
        Town town = towns.get(0);
        prefixSumA[0] = town.a;
        prefixSumAX[0] = (long)town.a * town.x;
        
        for(int i = 1; i < N; i++) {
            town = towns.get(i);
            
            prefixSumA[i] = prefixSumA[i - 1] + town.a;
            prefixSumAX[i] = prefixSumAX[i - 1] + (long)town.a * town.x;
        }
        
        for(int q = 0; q < Q; q++) {
            int xq = Integer.parseInt(br.readLine());
            
            int idx = binarySearch(xq);    //    xi >= xq인 i의 최솟값
            
            long answer = 0;
            
            if(idx == 0)    //    모든 x가 xq 이상일 경우
                answer = -partialSum(0, N - 1, prefixSumA) * xq + partialSum(0, N - 1, prefixSumAX);
            else if(idx == N)    //    모든 x가 xq보다 작을 경우
                answer = partialSum(0, N - 1, prefixSumA) * xq - partialSum(0, N - 1, prefixSumAX);
            else {    //    
                answer = partialSum(0, idx - 1, prefixSumA) * xq - partialSum(0, idx - 1, prefixSumAX);
                answer += (-partialSum(idx, N - 1, prefixSumA) * xq + partialSum(idx, N - 1, prefixSumAX));
            }
            
            sb.append(answer).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static long partialSum(int from, int to, long[] prefixSum) {
        if(from == 0)
            return prefixSum[to];
        
        return prefixSum[to] - prefixSum[from - 1];
    }
    
    private static int binarySearch(int xq) {
        int start = 0;
        int end = N - 1;
        int res = N;        //    모든 마을의 위치 x가 xq보다 작을 경우
        
        while(start <= end) {
            int mid = (start + end) / 2;
            
            Town town = towns.get(mid);
            
            if(town.x >= xq) {    //    mid번째 마을 위치 x가 xq보다 크거나 같을 경우
                res = mid;        //    일단 mid 저장
                end = mid - 1;    //    xq보다 크면서 더 작은 x가 존재하나 찾기
            }
            else                  //    mid번째 마을 위치 x가 xq보다 작을 경우
                start = mid + 1;  //    더 큰 x가 존재하나 찾기
        }
        
        return res;
    }
    
    private static class Town implements Comparable<Town> {
        public int a;    //    마을에 거주 중인 사람 수
        public int x;    //    마을 위치
        
        public Town(int a, int x) {
            this.a = a;
            this.x = x;
        }
        
        @Override
        public int compareTo(Town other) {
            return Integer.compare(this.x, other.x);
        }
    }
}    //    Main-class-end
```

### BOJ 14948 군대탈출하기

[14948번: 군대탈출하기](http://boj.ma/14948/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.LinkedList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    
    private static int[][] map;
    
    private static int max = 0;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        map = new int[N][M];
        for(int y = 0; y < N; y++) {
            st = new StringTokenizer(br.readLine());
            for(int x = 0; x < M; x++) {
                map[y][x] = Integer.parseInt(st.nextToken());
                max = Math.max(max, map[y][x]);
            }
        }
        
        System.out.println(parametricSearch());
    }    //    main-end
    
    private static int parametricSearch() {
        int start = 0;
        int end = max;
        int res = max;
        
        while(start <= end) {
            int mid = (start + end) / 2;
            
            if(bfs(mid)) {
                res = mid;
                end = mid - 1;
            }
            else
                start = mid + 1;
        }
        
        return res;
    }
    
    private static boolean bfs(int limit) {
        if(map[0][0] > limit || map[N - 1][M - 1] > limit)
            return false;
        
        Queue<int[]> queue = new LinkedList<>();
        
        //	visited[y][x][cnt] : 건너뛸 수 있는 횟수가 cnt번일때 (y, x) 방문 상태
        boolean[][][] visited = new boolean[N][M][2];
        visited[0][0][1] = true;
        queue.offer(new int[] {0, 0, 1});
        
        while(!queue.isEmpty()) {
            int[] cur = queue.poll();
            int cy = cur[0];
            int cx = cur[1];
            int ccnt = cur[2];
            
            for(int d = 0; d < 4; d++) {
                int ny = cy + dy[d];
                int nx = cx + dx[d];
                
                if(isIn(ny, nx)) {
                	if(ny == N - 1 && nx == M - 1)
                		return true;
                	
                	if(map[ny][nx] <= limit) {	//	뛰어넘을 필요 없을 경우
                		if(!visited[ny][nx][ccnt]) {
                			visited[ny][nx][ccnt] = true;
                			queue.offer(new int[] {ny, nx, ccnt});
                		}
                	}
                	else if(ccnt == 1) {	//	뛰어넘어야 하고 뛰어넘을 수 있을 경우
                		ny += dy[d];
                		nx += dx[d];	//	뛰어넘어서 도달할 칸
                		
                		if(ny == N - 1 && nx == M - 1)
                			return true;
                		
                		if(isIn(ny, nx) && map[ny][nx] <= limit && !visited[ny][nx][0]) {
                			visited[ny][nx][0] = true;
                			queue.offer(new int[] {ny, nx, 0});
                		}
                	}
                }
            }
        }
        
        return false;
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < N) && (0 <= x && x < M);
    }
}    //    Main-class-end
```