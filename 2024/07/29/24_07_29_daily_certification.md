# 24_07_29_daily_certification

```
[koreii] #211 데일리인증
2024729
알고리즘 & 코딩 테스트 대비
- 삼성 기출 풀이 (Deque, Simulation)
- Backtracking 문제 풀이
```

# Problem Solving (Algorithm & SQL)

### 삼성전자 2018년 하반기 오후 1번 바이러스 실험 (Success)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/training-field/frequent-problems/problems/virus-experiment/description?page=3&pageSize=20)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.Deque;
import java.util.LinkedList;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;	//	배지 크기 N * N
	private static int M;	//	초기 바이러스 개수
	private static int K;	//	사이클 수
	
	private static Deque<Integer>[][] virusMap;	//	virusMap[r][c] : (r, c)칸에 존재하는 바이러스 나이 큐, 오름차순으로 저장된다.
	private static Deque<Integer>[][] deadMap;	//	deadMap[r][c] : (r, c)칸에 존재하는 죽은 바이러스들의 나이 큐
	private static Deque<Integer>[][] newMap;	//	newMap[r][c] : (r, c)칸에 새로 번식할 바이러스들 임시로 모아둘 큐
	private static int[][] foodMap;				//	foodMap[r][c] : (r, c)칸에 존재하는 양분 양
	private static int[][] addMap;				//	addMap[r][c] : 매 사이클 끝날 때 마다 (r, c)에 추가되는 양분 양
	
	private static int ans = 0;	//	K 사이클 후 남아 있는 바이러스 수
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		virusMap = new LinkedList[N][N];
		deadMap = new LinkedList[N][N];
		newMap = new LinkedList[N][N];
		
		for(int r = 0; r < N; r++) {
			for(int c = 0; c < N; c++) {
				virusMap[r][c] = new LinkedList<>();
				deadMap[r][c] = new LinkedList<>();
				newMap[r][c] = new LinkedList<>();
			}
		}
		
		foodMap = new int[N][N];
		for(int r = 0; r < N; r++)
			Arrays.fill(foodMap[r], 5);
		
		addMap = new int[N][N];
		for(int r = 0; r < N; r++) {
			st = new StringTokenizer(br.readLine());
			for(int c = 0; c < N; c++)
				addMap[r][c] = Integer.parseInt(st.nextToken());
		}
		
		for(int v = 0; v < M; v++) {
			st = new StringTokenizer(br.readLine());
			
			int r = Integer.parseInt(st.nextToken()) - 1;
			int c = Integer.parseInt(st.nextToken()) - 1;
			int age = Integer.parseInt(st.nextToken());
			
			virusMap[r][c].offerLast(age);
		}
		
		for(int k = 0; k < K; k++)
			simulation();
		
		for(int r = 0; r < N; r++)
			for(int c = 0; c < N; c++)
				ans += virusMap[r][c].size();
		
		System.out.println(ans);
	}	//	main-end
	
	private static void simulation() {
		step1();
		step2();
		step3();
		step4();
	}
	
	private static void step1() {
		for(int r = 0; r < N; r++) {
			for(int c = 0; c < N; c++) {
				Deque<Integer> tmp = new LinkedList<>();	//	(r, c)칸에서 양분을 먹은 바이러스가 이동할 임시 큐
				
				while(!virusMap[r][c].isEmpty()) {
					int age = virusMap[r][c].peekFirst();	//	이번에 양분을 먹을 바이러스 나이, 나이 어린 순으로 먹기 시작
					
					if(age > foodMap[r][c])	//	이번 바이러스에서 양분이 부족할 경우, 이번 바이러스 이후 바이러스들은 모두 죽게 됨
						break;
					
					foodMap[r][c] -= age;
					virusMap[r][c].pollFirst();	//	해당 바이러스가 양분을 먹음
					tmp.offerLast(age + 1);	//	양분 먹은 바이러스 나이가 1 증가함, 나이가 많을 수록 뒤에 존재
				}
				
				while(!virusMap[r][c].isEmpty())	//	양분이 부족해서 죽은 바이러스 모음
					deadMap[r][c].offerFirst(virusMap[r][c].pollFirst());
				
				virusMap[r][c] = tmp;	//	살아남은 바이러스 큐로 대체
			}
		}
	}
	
	private static void step2() {
		for(int r = 0; r < N; r++) {
			for(int c = 0; c < N; c++) {
				while(!deadMap[r][c].isEmpty())		//	(r, c) 칸에서 죽은 모든 바이러스에 대하여
					foodMap[r][c] += (deadMap[r][c].pollFirst() / 2);	//	죽은 바이러스 나이 / 2 만큼 (r, c) 칸에 양분 추가
			}
		}
	}
	
	private static void step3() {
		for(int r = 0; r < N; r++) {
			for(int c = 0; c < N; c++) {
				int size = virusMap[r][c].size();
				
				for(int s = 0; s < size; s++) {
					int age = virusMap[r][c].pollFirst();	//	(r, c)칸에 존재하는 모든 바이러스에 대하여
					
					if(age % 5 == 0) {	//	해당 바이러스 나이가 5의 배수일 경우
						for(int d = 0; d < 8; d++) {
							int nr = r + dy[d];
							int nc = c + dx[d];	//	해당 바이러스 주변 8칸에 대해
							
							if(isIn(nr, nc))
								newMap[nr][nc].offerFirst(1);	//	나이 1인 바이러스 추가
						}
					}
					
					virusMap[r][c].offerLast(age);	//	바이러스 다시 집어넣기
				}
			}
		}
		
		for(int r = 0; r < N; r++) {
			for(int c = 0; c < N; c++) {
				while(!newMap[r][c].isEmpty())	//	(r, c)칸에 추가된 나이 1인 모든 바이러스들에 대해
					virusMap[r][c].offerFirst(newMap[r][c].pollFirst());	//	(r, c)칸에 추가
			}
		}
	}
	
	private static void step4() {
		for(int r = 0; r < N; r++) {
			for(int c = 0; c < N; c++)
				foodMap[r][c] += addMap[r][c];	//	모든 칸에 양분 보충
		}
	}
	
	private static final int[] dy = {0, 1, 1, 1, 0, -1, -1, -1};
	private static final int[] dx = {1, 1, 0, -1, -1, -1, 0, 1};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
}	//	Main-class-end
```

### BOJ 19942 다이어트

[](https://www.acmicpc.net/problem/19942)

```kotlin
import java.util.*
import java.io.*

class BOJ_19942 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var st: StringTokenizer

    private var n = 0
    private var mp = 0
    private var mf = 0
    private var ms = 0
    private var mv = 0

    private lateinit var pArr : IntArray
    private lateinit var fArr : IntArray
    private lateinit var sArr : IntArray
    private lateinit var vArr : IntArray
    private lateinit var cArr : IntArray

    private var minCost = Int.MAX_VALUE //  4가지 영양소 모두 최소치 이상으로 확보하는데 필요한 최소 비용
    private var minComb = ""            //  4가지 영양소 모두 최소치 이상으로 확보하는데 필요한 최소 비용의 조합

    fun solve() {
        n = br.readLine().toInt()
        st = StringTokenizer(br.readLine())
        mp = st.nextToken().toInt()
        mf = st.nextToken().toInt()
        ms = st.nextToken().toInt()
        mv = st.nextToken().toInt()

        pArr = IntArray(n + 1)
        fArr = IntArray(n + 1)
        sArr = IntArray(n + 1)
        vArr = IntArray(n + 1)
        cArr = IntArray(n + 1)

        repeat(n) {
            st = StringTokenizer(br.readLine())
            pArr[it + 1] = st.nextToken().toInt()
            fArr[it + 1] = st.nextToken().toInt()
            sArr[it + 1] = st.nextToken().toInt()
            vArr[it + 1] = st.nextToken().toInt()
            cArr[it + 1] = st.nextToken().toInt()
        }

        backtracking(0, 0, 0, 0, 0, 1, 0, StringBuilder(""))

        if(minCost == Int.MAX_VALUE)    //  조건 만족하는 조합 존재하지 않을 경우
            sb.append("-1\n")
        else
            sb.append("$minCost\n$minComb\n")

        print(sb)
    }

    private fun backtracking(nth : Int, p : Int, f : Int, s: Int, v : Int, start : Int, cost : Int, comb : StringBuilder) {
        if(cost >= minCost) //  이전에 결정된 최소 비용 이상일 경우
            return  //  최소 비용과 같으면 사전순에서 밀림, 최소 비용 초과면 의미가 없음

        if(ok(p, f, s, v)) {    //  조건 만족했을 경우
            if(cost < minCost) {    //  최소 비용이 갱신될 경우
                minCost = cost
                minComb = comb.toString()
            }

            return
        }

        if(nth == n)    //  n개 모두 더해도 조건 만족 못할 경우
            return

        for(i in start .. n) {
            val np = p + pArr[i]
            val nf = f + fArr[i]
            val ns = s + sArr[i]
            val nv = v + vArr[i]
            val ncost = cost + cArr[i]
            val ncomb = StringBuilder(comb).append("$i ")

            backtracking(nth + 1, np, nf, ns, nv, i + 1, ncost, ncomb)
        }
    }

    //  4가지 영양소 모두 최소치 이상 확보했으면 true
    private fun ok(p : Int, f : Int, s : Int, v : Int) : Boolean {
        return (p >= mp) && (f >= mf) && (s >= ms) && (v >= mv)
    }
}

fun main() {
    BOJ_19942().solve()
}
```