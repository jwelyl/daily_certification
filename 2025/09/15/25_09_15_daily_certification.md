# 25_09_15_daily_certification

# Problem Solving (Algorithm & SQL)

### SW Expert Academy 1767. 프로세서 연결하기

[SW Expert Academy](https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV4suNtaXFEDFAUf)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;
 
public class Solution {
    private static final int EMPTY = 0;
    private static final int CORE = 1;
     
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
     
    private static int T;   //  테스트케이스 개수
    private static int N;   //  보드 크기 N * N
    private static int[][] board;
     
    //  가장자리에 있지 않아서 전원 연결해야 하는 코어 좌표 리스트
    private static final List<int[]> cores = new ArrayList<>();
     
    private static boolean find = false;
    private static int answer = Integer.MAX_VALUE;
     
    public static void main(String[] args) throws IOException {
        T = Integer.parseInt(br.readLine().trim());
         
        for(int tc = 1; tc <= T; tc++) {
            N = Integer.parseInt(br.readLine().trim());
            board = new int[N][N];
            cores.clear();
            find = false;
            answer = Integer.MAX_VALUE;
             
            for(int y = 0; y < N; y++) {
                st = new StringTokenizer(br.readLine());
                for(int x = 0; x < N; x++) {
                    board[y][x] = Integer.parseInt(st.nextToken());
                     
                    //  전선을 연결해야 하는 코어일 경우
                    if(board[y][x] == CORE && !isBoundary(y, x))
                        cores.add(new int[] {y, x});
                }
            }
             
            if(cores.size() == 0)   //  코어가 아예 없거나 이미 모든 코어가 전원이 들어와있는 경우
                answer = 0;
            else {
                for(int cnt = cores.size(); cnt >= 1; cnt--) {   //  cnt개의 코어를 놓을 수 있다고 가정
                    dfs(0, 0, 0, 0, cnt);   //  0번째 코어부터 놓아보기
                     
                    if(find)    //  최대로 cnt개 놓는 방법을 찾은 경우
                        break;
                }
                 
                if(!find)   //  만약 이미 가장자리에 있는 코어를 제외하고 하나도 연결할 수 없을 경우
                    answer = 0; //  최대로 연결된 코어 제외하고 전선 필요 없으므로 0임
            }
             
            sb.append("#").append(tc).append(" ").append(answer).append("\n");
        }
         
        System.out.print(sb);
    }   //  main-end
     
    private static void dfs(int nth, int put, int missed, int sum, int targetCnt) {
        if(put == targetCnt) {  //  목표 숫자의 코어를 전부 놓은 경우
            if(!find)           //  targetCnt 개수 놓는 방법 최초로 찾은 경우
                find = true;    
             
            answer = Math.min(answer, sum); //  전선 길이 합 최솟값 갱신
            return;
        }
         
        if(nth == cores.size()) //  모든 코어 다 확인해봤지만 원하는 갯수만큼 놓지 못한 경우
            return;
         
        int[] core = cores.get(nth);    //  현재 코어
        int cy = core[0];
        int cx = core[1];
         
        boolean canPut = false; //  현재 코어에 전선을 연결할 수 있으면 true
         
        for(int d = 0; d < 4; d++) {
            if(canPut(cy, cx, d)) { //  d 방향으로 전선 놓을 수 있을 경우
                canPut = true;
                int len = put(cy, cx, d, true); //  d 방향으로 놓은 전선 길이
                dfs(nth + 1, put + 1, missed, sum + len, targetCnt);    //  다른 프로세서 놓기 시도
                put(cy, cx, d, false);  //  전선 다시 치우기
            }
        }
         
        //  현재 프로세서에 전선을 연결하지 않는다고 가정
        int allowedMiss = cores.size() - targetCnt; //  targetCnt만큼 코어 연결하기 위해 연결하지 않아도 되는 최대 개수
         
        if(missed + 1 <= allowedMiss)    //  이번 프로세서를 연결하지 않아도 될 경우
            dfs(nth + 1, put, missed + 1, sum, targetCnt);
    }
     
    //  (y, x)에 존재하는 코어를 dir 방향으로 전선에 연결할 수 있는지 확인
    private static boolean canPut(int y, int x, int dir) {
        for(int k = 1; ; k++) {
            int ny = y + dy[dir] * k;
            int nx = x + dx[dir] * k;
             
            if(isBoundary(ny, nx) && board[ny][nx] == EMPTY)    //  가장자리에 도달했고, 가장자리에 다른 코어나 전선이 없을 경우
                break;
             
            if(board[ny][nx] != EMPTY)  //  해당 방향에 이미 다른 코어나 전선이 있을 경우
                return false;
        }
         
        return true;
    }
     
    //  (y, x)에서 dir 방향으로 전선을 놓거나(put = true), 치움(put = false), 그 때 전선 길이 반환
    private static int put(int y, int x, int dir, boolean put) {
        int res = 0;
         
        for(int k = 1; ; k++) {
            int ny = y + dy[dir] * k;
            int nx = x + dx[dir] * k;
             
            board[ny][nx] = put ? CORE : EMPTY;
            res++;
             
            if(isBoundary(ny, nx))
                break;
        }
         
        return res;
    }
     
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
     
    private static boolean isBoundary(int y, int x) {
        return y == 0 || y == N - 1 || x == 0 || x == N - 1;
    }
}   //  Main-class-end
```