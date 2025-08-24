# 25_08_24_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 25978 2차원 배열 다중 업데이트 다중 합

[25978번: 2차원 배열 다중 업데이트 다중 합](http://boj.ma/25978/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;    //    배열 크기 N * N
    private static int M;    //    질의 개수
    
    private static long[][] prefixSum;
    private static long[][] diffSum;
    
    private static boolean q1End = false;    //    1번 종류 질의가 끝나면 true
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        prefixSum = new long[N + 2][N + 2];
        diffSum = new long[N + 2][N + 2];
        
        for(int y = 1; y <= N; y++) {
            st = new StringTokenizer(br.readLine());
            for(int x = 1; x <= N; x++)
                prefixSum[y][x] = Integer.parseInt(st.nextToken());
        }
        
        for(int q = 0; q < M; q++) {
            st = new StringTokenizer(br.readLine());
            int opt = Integer.parseInt(st.nextToken());
            int y1 = Integer.parseInt(st.nextToken()) + 1;
            int x1 = Integer.parseInt(st.nextToken()) + 1;
            int y2 = Integer.parseInt(st.nextToken()) + 1;
            int x2 = Integer.parseInt(st.nextToken()) + 1;
            
            if(opt == 1) {
                int k = Integer.parseInt(st.nextToken());
                
                diffSum[y1][x1] += k;
                diffSum[y1][x2 + 1] -= k;
                diffSum[y2 + 1][x1] -= k;
                diffSum[y2 + 1][x2 + 1] += k;
            }
            else {
                if(!q1End) {    //    2번 질의가 처음 시작될 경우
                    q1End = true;
                    
                    //    더 이상 배열 변경 없으므로 누적합 한번만 구하면 된다.
                    for(int y = 1; y <= N; y++) {
                        for(int x = 1; x <= N; x++) {
                            diffSum[y][x] = diffSum[y - 1][x] + diffSum[y][x - 1] - diffSum[y - 1][x - 1] + diffSum[y][x];
                            prefixSum[y][x] = prefixSum[y - 1][x] + prefixSum[y][x - 1] - prefixSum[y - 1][x - 1] + prefixSum[y][x];
                            prefixSum[y][x] += diffSum[y][x];
                        }
                    }
                }
                
                //    좌상단 (y1, x1), 우하단 (y2, x2)인 부분행렬의 부분합 출력
                sb.append(prefixSum[y2][x2] - prefixSum[y2][x1 - 1] - prefixSum[y1 - 1][x2] + prefixSum[y1 - 1][x1 - 1]).append("\n");
            }
        }
        
         System.out.print(sb);
    }    //    main-end
}    //    Main-class-end
```