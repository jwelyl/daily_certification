# 24_09_20_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 18430 무기 공학

[](https://www.acmicpc.net/problem/18430)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;          //    나무판 행, 열 크기
    
    private static int[][] strBoard;       //    강도
    private static boolean[][] visited;    //    방문 처리
    
    private static int maxStr = 0;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        strBoard = new int[N][M];
        visited = new boolean[N][M];
        
        for(int r = 0; r < N; r++) {
            st = new StringTokenizer(br.readLine());
            for(int c = 0; c < M; c++)
                strBoard[r][c] = Integer.parseInt(st.nextToken());
        }
        
        dfs(0, 0);
        
        System.out.println(maxStr);
    }    //    main-end
    
    private static void dfs(int nth, int str) {
        if(nth == N * M) {    //    끝까지 다 놔봤을 경우
            maxStr = Math.max(maxStr, str);
            return;
        }
        
        int cy = nth / M;
        int cx = nth % M;    //    중심 좌표 (cy, cx)
        
        if(!visited[cy][cx]) {   //    (cy, cx)에 다른 부메랑이 놓여있지 않을 경우
            //    (cy, cx)를 중심으로 4가지 부메랑 놓아보기
            for(int d = 0; d < 4; d++) {
                int ny1 = cy + dy[d][0];
                int nx1 = cx + dx[d][0];
                int ny2 = cy + dy[d][1];
                int nx2 = cx + dx[d][1];
                
                //    부메랑 나머지 두 칸도 범위 안이고, 다른 부메랑이 놓여있지 않을 경우
                if(isIn(ny1, nx1) && isIn(ny2, nx2) && !visited[ny1][nx1] && !visited[ny2][nx2]) {
                    visited[cy][cx] = true;
                    visited[ny1][nx1] = true;
                    visited[ny2][nx2] = true;    //    해당 부메랑이 차지하는 칸 방문 처리
                    
                    //    해당 부메랑 놓고 다음 칸 넘어가보기
                    dfs(nth + 1, str + 2 * strBoard[cy][cx] + strBoard[ny1][nx1] + strBoard[ny2][nx2]);
                
                    visited[cy][cx] = false;
                    visited[ny1][nx1] = false;
                    visited[ny2][nx2] = false;    //    해당 부메랑이 차지하는 칸 방문 해제
                }
            }
        }
        
        dfs(nth + 1, str);    //    (cy, cx)에 부메랑 놓지 않고 다음 칸으로 넘어가보기
    }
    
    private static final int[][] dy = {{0, 1}, {0, -1}, {-1, 0}, {1, 0}};
    private static final int[][] dx = {{-1, 0}, {-1, 0}, {0, 1}, {0, 1}};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < N) && (0 <= x && x < M);
    }
}    //    Main-class-end
```

### BOJ 1477 휴게소 세우기

[](https://www.acmicpc.net/problem/1477)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    이미 놓여진 휴게소 수
    private static int M;    //    더 세우려는 휴게소 수
    private static int L;    //    고속도로 길이
    
    private static int[] pos;    //   pos[i] : i번째 휴게소 위치 (고속도로 시작점으로부터 얼마나 떨어져 있는지)
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        L = Integer.parseInt(st.nextToken());
        
        pos = new int[N + 2];
        
        st = new StringTokenizer(br.readLine());
        for(int i = 1; i <= N; i++)
            pos[i] = Integer.parseInt(st.nextToken());
        pos[N + 1] = L;
        
        Arrays.sort(pos);    //    휴게소 위치를 고속도로 시작부터 가까운 순으로 정렬, 0은 시작점, N + 1은 끝점
    
        System.out.println(parametricSearch());
    }    //    main-end
    
    private static int parametricSearch() {
        int start = 1;
        int end = L;
        int ret = L;    //    M개의 휴게소를 추가로 건설했을 때, 휴게소가 없는 거리 중 가장 큰 거리의 최솟값
        
        while(start <= end) {
            int mid = (start + end) / 2;    //    휴게소가 없는 거리 중 가장 큰 거리가 mid라고 가정
            int cnt = cnt(mid);             //    휴게소가 없는 거리 중 가장 큰 거리가 mid임을 만족시키기 위해 설치해야 하는 휴게소 개수

            if(cnt <= M) {            		  //    M개 이하 설치해서 조건 만족할 경우
                ret = mid;            		  //    일단 ret 값 저장
                end = mid - 1;        		  //    더 작은 값으로 조건 만족 가능한지 확인
            }
            else			                  //    M개보다 더 설치해야 mid 이상의 거리가 안 생김, mid가 너무 작은 경우
                start = mid + 1;       	      //    더 큰 값으로 조건 만족 가능한지 확인
        }
        
        return ret;
    }
    
    private static int cnt(int dist) {
        int ret = 0;
        
        for(int i = 0; i <= N; i++) {
            int curDist = pos[i + 1] - pos[i];    //    현재 휴게소가 없는 거리

            if(curDist % dist == 0)    //    나누어 떨어질 경우, 양 끝에 설치 불가하므로 1개는 설치 못함
                ret += curDist / dist - 1;
            else
                ret += curDist / dist;
        }

        return ret;
    }
}    //    Main-class-end
```