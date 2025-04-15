# 25_04_14_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 10429 QUENTO

[10429번: QUENTO](http://boj.ma/10429/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
    private static final int ADD =  1;
    private static final int SUB = -1;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;    //    만들어야 할 수
    private static int M;    //    사용할 수의 개수
    private static final char[][] board = new char[3][3];
    
    private static boolean ok = false;    //    M개의 수로 N을 만들수 있으면 true
    private static final int[][] path = new int[10][2];
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        for(int y = 0; y < 3; y++)
            board[y] = br.readLine().toCharArray();
        
        for(int pos = 0; pos <= 8; pos += 2) {
            int y = pos / 3;
            int x = pos % 3;    //    숫자가 놓인 칸 (y, x)에서 시작
            
            dfs(1, y, x, new int[10], new boolean[3][3]);
            
            if(ok) {
                sb.append("1\n");
                for(int i = 1; i <= 2 * M - 1; i++)
                    sb.append(path[i][0]).append(" ").append(path[i][1]).append("\n");
             
                System.out.print(sb);
                return;
            }
        }
        
        System.out.println(0);
    }    //    main-end
    
    private static void dfs(int nth, int y, int x, int[] res, boolean[][] visited) {
        if(ok)
            return;
        
        visited[y][x] = true;
        path[nth][0] = y;
        path[nth][1] = x;
        
        if(nth % 2 == 1) {    //    숫자일 경우
            int digit = board[y][x] - '0';
            
            if(res[nth - 1] == ADD)
                res[nth] = res[nth - 2] + digit;
            else if(res[nth - 1] == SUB)
                res[nth] = res[nth - 2] - digit;
            else
                res[nth] = digit;
            
            if(nth == 2 * M - 1) {    //    M게 수를 다 사용했을 경우
                if(res[nth] == N) {
                    ok = true;
                    return;
                }
            }
        }
        else    //    연산자인 경우
            res[nth] = board[y][x] == '+' ? ADD : SUB;
        
        for(int d = 0; d < 4; d++) {
            int ny = y + dy[d];
            int nx = x + dx[d];
            
            if(isIn(ny, nx) && !visited[ny][nx])
                dfs(nth + 1, ny, nx, res, visited);
        }
        
        visited[y][x] = false;
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < 3) && (0 <= x && x < 3);
    }
}    //    Main-class-end
```