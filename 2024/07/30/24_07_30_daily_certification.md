# 24_07_30_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 9944 **NxM 보드 완주하기**

[](https://www.acmicpc.net/problem/9944)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final char WALL = '*';
	private static final char EMPTY = '.';
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int TC;
	private static int N;
	private static int M;
	
	private static char[][] board;
	private static int empty = 0;
	private static int minCnt = Integer.MAX_VALUE;
	
	public static void main(String[] args) throws IOException {
		while(true) {
			String input = br.readLine();
			
			if(input == null) {
				System.out.print(sb);
				break;
			}
			
			TC++;
			minCnt = Integer.MAX_VALUE;
			
			st = new StringTokenizer(input);
			N = Integer.parseInt(st.nextToken());
			M = Integer.parseInt(st.nextToken());
			empty = N * M;	//	초기 빈칸 개수
			
			board = new char[N][M];
			
			for(int r = 0; r < N; r++) {
				board[r] = br.readLine().toCharArray();
				for(int c = 0; c < M; c++) {
					if(board[r][c] == WALL)
						empty--;
				}
			}
			
			for(int r = 0; r < N; r++) {
				for(int c = 0; c < M; c++) {
					if(board[r][c] == EMPTY) {	//	해당 칸이 빈 칸일 경우
						board[r][c] = WALL;		//	해당 칸 방문 처리
						backtracking(r, c, empty - 1, 0);	//	해당 칸에서부터 이동 시작
						board[r][c] = EMPTY;	//	해당 칸 방문 처리 취소
					}
				}
			}
			
			sb.append("Case ").append(TC).append(": ").append(minCnt == Integer.MAX_VALUE ? -1 : minCnt).append("\n");
		}
	}	//	main-end
	
	private static void backtracking(int y, int x, int empty, int cnt) {
		if(empty == 0) {	//	완주했을 경우
			minCnt = Math.min(minCnt, cnt);	//	최소 이동 횟수 갱신
			return;
		}
		
		int nempty = empty;
		
		for(int d = 0; d < 4; d++) {
			int ny = y + dy[d];
			int nx = x + dx[d];	//	d 방향으로 이동할 때 다음 칸
			
			if(!isIn(ny, nx) || board[ny][nx] == WALL)	//	d 방향으로는 갈 수 없을 경우
				continue;

			int ey = ny;
			int ex = nx;	//	갈 수 있는 마지막 칸 좌표
			
			for(; isIn(ey, ex) && board[ey][ex] == EMPTY; ey += dy[d], ex += dx[d]) {	//	갈 수 있는 칸까지 d 방향으로 이동
				nempty--;					//	빈 칸 개수 감소
				board[ey][ex] = WALL;		//	이동한 칸 방문 처리
			}
			
			ey -= dy[d];
			ex -= dx[d];	//	초과한 칸 롤백
			
			backtracking(ey, ex, nempty, cnt + 1);	//	이동한 마지막 칸에서 다음 이동 시작
			
			for(; (ey != y || ex != x); ey -= dy[d], ex -= dx[d]) {	//	시작 칸 직전까지 d 방향의 역방향으로 이동
				nempty++;					//	빈 칸 개수 복귀
				board[ey][ex] = EMPTY;		//	이동한 칸 방문 처리 취소
			}
		}	
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}
}	//	Main-class-end
```

### BOJ 12004 **Closing the Farm (Silver)**

[](https://www.acmicpc.net/problem/12004)

```kotlin
import java.util.*
import java.io.*

const val NONE = -1 //  어떤 정점 v가 어떤 분리 집합에도 포함되지 않을 경우 ds[v] = NONE임, closed 상태의 v의 경우 ds[v] = NONE임

class BOJ_12004 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var st: StringTokenizer

    private var n = 0   //  정점 개수
    private var m = 0   //  간선 개수

    private var dsCnt = 0   //  전체 분리 집합 개수, 1개면 현재 개방된 모든 정점들이 연결된 것임

    private lateinit var graph : Array<ArrayList<Int>>
    private lateinit var parents : IntArray      //  분리집합, parents[i] : i가 속한 분리 집합에서 i의 부모
    private lateinit var order : IntArray   //  폐쇄하는 순서, 개방하는 순서의 역순

    private val ans = Stack<String>()

    fun solve() {
        st = StringTokenizer(br.readLine())
        n = st.nextToken().toInt()
        m = st.nextToken().toInt()

        graph = Array(n + 1) { ArrayList() }
        parents = IntArray(n + 1) { NONE }
        order = IntArray(n + 1)

        repeat(m) {
            st = StringTokenizer(br.readLine())
            val v1 = st.nextToken().toInt()
            val v2 = st.nextToken().toInt()

            graph[v1].add(v2)
            graph[v2].add(v1)
        }

        repeat(n) {
            val v = br.readLine().toInt()

            order[it + 1] = v
        }

        for(i in n downTo 1) {
            val v = order[i]    //  개방할 정점

            parents[v] = v
            dsCnt++ //  일단 단독 분리집합 만들기

            for(nv in graph[v]) {   //  그래프 상에서 이웃한 정점들에 대하여
                if(parents[nv] == NONE) //  이웃한 정점이 아직 어떤 분리 집합에도 속하지 않을 경우 skip
                    continue

                if(findDS(v) != findDS(nv))   //  v와 nv가 서로 다른 분리 집합에 속할 경우
                    union(v, nv)    //  두 분리 집합 합치기
            }

            ans.push(if(dsCnt == 1) "YES\n" else "NO\n")
        }

        while(ans.isNotEmpty())
            sb.append(ans.pop())

        print(sb)
    }

    //  v가 속한 분리 집합의 우두머리 구하기
    private fun findDS(v : Int) : Int {
        if(parents[v] == NONE || parents[v] == v)   //  NONE일 경우 어떤 분리 집합에도 속하지 않음, v일 경우 v 혼자 분리 집합 이룸
            return parents[v]

        parents[v] = findDS(parents[v])
        return parents[v]
    }

    private fun union(v1 : Int, v2 : Int) {
        val v1DS = findDS(v1)
        val v2DS = findDS(v2)

        parents[v1DS] = v2DS    //  두 분리 집합이 합쳐짐
        dsCnt-- //  분리 집합 개수 1개 감소
    }
}

fun main() {
    BOJ_12004().solve()
}
```