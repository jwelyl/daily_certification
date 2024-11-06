# 24_11_06_daily_certification

```
[#310 koreii] 데일리인증 20241105
1. Java Multithread, Concurrency
- Thread 직접 사용할때의 문제점
- Thread Pool
2. 코딩 테스트 대비 복습문제 풀이
- Bruteforcing, Simulation (17779 게리맨더링 2)
3. 라이브 코딩테스트 연습
- HashSet, equals, hashCode (HackerRank Queen's Attack II)
```

# Problem Solving (Algorithm & SQL)

### BOJ 17779 게리맨더링 2

[](https://www.acmicpc.net/problem/17779)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;			//	재현시 크기 N
	private static int[][] map;		//	map[i][j] : 재현시의 (i, j) 구역의 인구수
	
	private static int maxWardCnt;		//	인구 가장 많은 선거구 인구수
	private static int minWardCnt;		//	인구 가장 적은 선거구 인구수
	private static int minDiff = Integer.MAX_VALUE;	//	가장 인구가 많은 선거구와 적은 선거구의 인구 차이의 최솟값
	
	private static final int[] numOfRegions = new int[5];	//	numOfRegions[i] : i + 1번째 선거구에 속한 인구수
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		map = new int[N + 1][N + 1];
		
		for(int i = 1; i <= N; i++) {
			st = new StringTokenizer(br.readLine());
			
			for(int j = 1; j <= N; j++)
				map[i][j] = Integer.parseInt(st.nextToken());
		}	//	input-end
		
		simulation();
		
		System.out.println(minDiff);
	} //	main-end
	
	//	매번 x, y, d1, d2가 결정될 때마다 numOfRegions와 ward를 초기화함
	private static void reset() {
		Arrays.fill(numOfRegions, 0);
		maxWardCnt = 0;
		minWardCnt = Integer.MAX_VALUE;
	}
	
	private static void simulation() {
		for(int x = 1; x <= N - 2; x++) {
			for(int y = 2; y <= N - 1; y++) {
				for(int d1 = 1; d1 <= N - 2; d1++) {
					for(int d2 = 1; d2 <= N - 2; d2++) {
						if(x + d1 > N || y - d1 < 1 || x + d1 + d2 > N || y - d1 + d2  > N || x + d2 > N || y + d2 > N)
							continue;
						
						reset();
						setWards(x, y, d1, d2);
						
						for(int i = 0; i < 5; i++) {
							if(numOfRegions[i] > maxWardCnt)
								maxWardCnt = numOfRegions[i];
							if(numOfRegions[i] < minWardCnt)
								minWardCnt = numOfRegions[i];
						}
						
						if(minWardCnt == 0)	//	인구가 0인 선거구가 있을 경우
							continue;
						
						minDiff = Math.min(minDiff, maxWardCnt - minWardCnt);
					}
				}
			}
		}
	}	//	simulation-end
	
	//	x, y, d1, d2가 정해졌을 때 재현시의 모든 구역의 선거구를 결정함
	private static void setWards(int x, int y, int d1, int d2) {
		for(int r = 1; r <= N; r++) {
			for(int c = 1; c <= N; c++) {				
				//	선거구 5에 해당하는 부분
				//	선분 1. (x, y)와 (x + d1, y - d1) 잇는 선분 r = -c + y + x
				//	선분 2. (x, y)와 (x + d2, y + d2) 잇는 선분 r = c - y + x
				//	선분 3. (x + d1, y - d1)와 (x + d1 + d2, y - d1 + d2) 잇는 선분 r = c - y + x + 2 * d1
				//	선분 4. (x + d2, y + d2)와 (x + d1 + d2, y - d1 + d2) 잇는 선분 r = -c + y + x + 2 * d2
				if((r >= -c + y + x) && (r <= -c + y + x + 2 * d2) && 
						(r >= c - y + x) && (r <= c - y + x + 2 * d1)) 
					numOfRegions[4] += map[r][c];	//	5번 선거구 인구 추가
				//	선거구 1에 해당하는 부분
				else if((1 <= r && r < x + d1) && (1 <= c && c <= y) && (r < -c + y + x)) 
					numOfRegions[0] += map[r][c];
				//	선거구 2에 해당하는 부분
				else if((1 <= r && r <= x + d2) && (y < c && c <= N) && (r < c - y + x)) 
					numOfRegions[1] += map[r][c];
				//	선거구 3에 해당하는 부분
				else if((x + d1 <= r && r <= N) && (1 <= c && c < y - d1 + d2) && (r > c - y +x + 2 * d1)) 
					numOfRegions[2] += map[r][c];
				//	선거구 4에 해당하는 부분
				else if((x + d2 < r && r <= N) && (y - d1 + d2 <= c && c <= N) && (r > -c + y + x + 2 * d2)) 
					numOfRegions[3] += map[r][c];
			}
		}
	}	//	setWards-end
} //	Main-class-end
```

### HackerRank Queen’s Attack II

[](https://www.acmicpc.net/problem/17779)

```java
import java.io.*;
import java.math.*;
import java.security.*;
import java.text.*;
import java.util.*;
import java.util.concurrent.*;
import java.util.function.*;
import java.util.regex.*;
import java.util.stream.*;
import static java.util.stream.Collectors.joining;
import static java.util.stream.Collectors.toList;

class Result {
    private static class Pos {
        public int r;
        public int c;
        
        public Pos(int r, int c) {
            this.r = r;
            this.c = c;
        }
        
        @Override
        public boolean equals(Object o) {
            if(o == this)
                return true;
            if(o == null || !(o instanceof Pos))
                return false;
            
            Pos p = (Pos)o;
            if(p.r != r || p.c != c)
                return false;
            
            return true;
        }
        
        @Override
        public int hashCode() {
            return Objects.hash(r, c);
        }
    }
    
    private static boolean isIn(int r, int c, int n) {
        return (1 <= r && r <= n) && (1 <= c && c <= n);
    }
    
    private static int[] dr = {0, 1, 1, 1, 0, -1, -1, -1};
    private static int[] dc = {1, 1, 0, -1, -1, -1, 0, 1};
    

    /*
     * Complete the 'queensAttack' function below.
     *
     * The function is expected to return an INTEGER.
     * The function accepts following parameters:
     *  1. INTEGER n
     *  2. INTEGER k
     *  3. INTEGER r_q
     *  4. INTEGER c_q
     *  5. 2D_INTEGER_ARRAY obstacles
     */

    public static int queensAttack(int n, int k, int r_q, int c_q, List<List<Integer>> obstacles) {
    // Write your code here
        int answer = 0;
    
        Set<Pos> set = new HashSet<>();
        for(List<Integer> obstacle : obstacles) {
            int r = obstacle.get(0);
            int c = obstacle.get(1);
            
            set.add(new Pos(r, c));
        }
        
        for(int d = 0; d < 8; d++) {
            for(int i = 1; ; i++) {
                int rnext = r_q + dr[d] * i;
                int cnext = c_q + dc[d] * i;
                
                Pos pos = new Pos(rnext, cnext);
                
                if(set.contains(pos) || !isIn(rnext, cnext, n))
                    break; 
                    
                answer++;               
            }
        }
    
        return answer;
    }

}

public class Solution {
    public static void main(String[] args) throws IOException {
        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in));
        BufferedWriter bufferedWriter = new BufferedWriter(new FileWriter(System.getenv("OUTPUT_PATH")));

        String[] firstMultipleInput = bufferedReader.readLine().replaceAll("\\s+$", "").split(" ");

        int n = Integer.parseInt(firstMultipleInput[0]);

        int k = Integer.parseInt(firstMultipleInput[1]);

        String[] secondMultipleInput = bufferedReader.readLine().replaceAll("\\s+$", "").split(" ");

        int r_q = Integer.parseInt(secondMultipleInput[0]);

        int c_q = Integer.parseInt(secondMultipleInput[1]);

        List<List<Integer>> obstacles = new ArrayList<>();

        IntStream.range(0, k).forEach(i -> {
            try {
                obstacles.add(
                    Stream.of(bufferedReader.readLine().replaceAll("\\s+$", "").split(" "))
                        .map(Integer::parseInt)
                        .collect(toList())
                );
            } catch (IOException ex) {
                throw new RuntimeException(ex);
            }
        });

        int result = Result.queensAttack(n, k, r_q, c_q, obstacles);

        bufferedWriter.write(String.valueOf(result));
        bufferedWriter.newLine();

        bufferedReader.close();
        bufferedWriter.close();
    }
}

```