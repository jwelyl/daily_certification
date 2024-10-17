# 24_10_17_daily_certification

```
[#291 koreii] 데일리인증 20241017
1. 스타트업 면접 + 후기 작성
2. Spring 복습
- IoC/DI
- AOP
3. JPA 복습
4. 알고리즘 & 코딩 테스트 대비 (복습)
- Backtracking, MITM (1799 비숍)
- Two Pointer, Sorting (1253 좋다)
```

# Problem Solving (Algorithm & SQL)

### BOJ 1799 비숍

[](https://www.acmicpc.net/problem/1799)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static boolean[][] chess;    //    chess[y][x] : true면 (y, x)에 놓을 수 있고, false면 놓을 수 없음
    private static boolean[][] visited;
    
    private static int maxBlack = 0;    //    검은색 칸에 놓을 수 있는 최대 비숍 수
    private static int maxWhite = 0;    //    흰색 칸에 놓을 수 있는 최대 비숍 수
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        chess = new boolean[N][N];
        visited = new boolean[N][N];
        
        for(int r = 0; r < N; r++) {
            st = new StringTokenizer(br.readLine());
            for(int c = 0; c < N; c++)
                chess[r][c] = st.nextToken().equals("1");
        }
        
        //    (0, 0)을 검은색 칸이라 가정하면 y + x가 짝수인 칸은 검은색, 홀수인 칸은 흰색 칸이 된다.
        dfs(0, 0, true);     //    검은색 칸에 놓을 수 있는 최대 비숍 수 찾기
        dfs(1, 0, false);    //    흰색 칸에 놓을 수 있는 최대 비숍 수 찾기
        
        System.out.println(maxBlack + maxWhite);
    }    //    main-end
    
    private static void dfs(int num, int cnt, boolean black) {
        if(num >= N * N) {    //    모든 검은색(흰색) 칸을 다 확인했을 경우
            if(black)
                maxBlack = Math.max(maxBlack, cnt);
            else
                maxWhite = Math.max(maxWhite, cnt);
            
            return;
        }
        
        int cr = num / N;
        int cc = num % N;    //    현재 칸
        
        int nnum = num + 2;
        
        if(N % 2 == 0) {
        	if(cc == N - 2)
        		nnum = num + 3;
        	else if(cc == N - 1)
        		nnum = num + 1;
        }
        
        if(!chess[cr][cc]) {    //    현재 칸에 비숍 놓을 수 없을 경우
        	dfs(nnum, cnt, black);
            return;
        }
            
        if(canPut(cr, cc)) {    //    (cr, cc)에 비숍 놓을 수 있을 경우
            visited[cr][cc] = true;            //    (cr, cc)에 비숍 놓기
            dfs(nnum, cnt + 1, black);      //    놓인 비숍 1개 증가 후 다음 칸 확인
            visited[cr][cc] = false;           //    (cr, cc)에 놓았던 비숍 치우기
        }
        
        dfs(nnum, cnt, black);    //    (cr, cc)에 비숍 놓지 않고 다음 칸 확인
    }
    
    private static boolean canPut(int r, int c) {
        for(int d = 0; d < 2; d++) {
            for(int k = 1; ; k++) {
                int nr = r + dr[d] * k;
                int nc = c + dc[d] * k;
                
                if(!isIn(nr, nc))
                    break;
                
                if(visited[nr][nc])
                    return false;
            }
        }
        
        return true;
    }
    
    private static final int[] dr = {-1, -1};
    private static final int[] dc = {-1, 1};
    
    private static boolean isIn(int r, int c) {
        return 0 <= r && r < N && 0 <= c && c < N;
    }
}    //    Main-class-end
```

### BOJ 6198 옥상 정원 꾸미기

[](https://www.acmicpc.net/problem/6198)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.Stack;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static int N;				//	건물 개수
    private static int[] buildings;		//	buildings[i] : i번째 건물의 높이
    
    //	rights[i] : i번째 건물의 오른쪽 건물 중 최초로 i번째 건물 높이 이상인 건물의 index
    //	i번째 건물에서는 rights[i]에 있는 건물 포함 그 오른쪽에 있는 건물은 볼 수 없음
    private static int[] rights;
    
    private static final Stack<int[]> stack = new Stack<>();
    
    private static long ans = 0L;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
       
        buildings = new int[N];
        rights = new int[N];
        Arrays.fill(rights, N);
        
        for(int bIdx = 0; bIdx < N; bIdx++)
        	buildings[bIdx] = Integer.parseInt(br.readLine());
        
        //	오른쪽 건물부터
        for(int bIdx = N - 1; bIdx >= 0; bIdx--) {
        	int bh = buildings[bIdx];	//	bIdx 건물 높이
        	
        	while(!stack.isEmpty()) {
        		int rh  = stack.peek()[0];		//	오른쪽에 있는 빌딩의 높이
        		int rIdx = stack.peek()[1];		//	오른쪽에 있는 빌딩의 index
        		
        		if(rh >= bh) {	//	rIdx의 빌딩이 bIdx의 빌딩 높이 이상일 경우
        			rights[bIdx] = rIdx;
        			break;
        		}
        		
        		stack.pop();	//	bIdx에서 볼 수 있는 건물들을 스택에서 지우기
        	}
        	
        	stack.push(new int[] {bh, bIdx});
        }
        
        for(int bIdx = 0; bIdx < N; bIdx++) {
        	//	bIdx의 오른쪽에 있는 건물 수 - bIdx에서 볼 수 없는 건물 수
        	int cnt = (N - 1 - bIdx) - (N - rights[bIdx]);
        	
        	ans += cnt;
        }
        
        System.out.println(ans);
    }    //    main-end
}    //    Main-class-end
```