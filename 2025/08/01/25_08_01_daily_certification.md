# 25_08_01_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 10836 여왕벌

[10836번: 여왕벌](http://boj.ma/10836/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int M;    //    벌집 크기 M * M
    private static int N;    //    성장 횟수
    
    private static int[][] sum;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        M = Integer.parseInt(st.nextToken());
        N = Integer.parseInt(st.nextToken());
        
        sum = new int[M + 1][M + 1];
        for(int i = 0; i < N; i++) {
            st = new StringTokenizer(br.readLine());
            int cnt0 = Integer.parseInt(st.nextToken());
            int cnt1 = Integer.parseInt(st.nextToken());
            int cnt2 = Integer.parseInt(st.nextToken());
            
            for(int y = M; y >= 1; y--) {
                if(cnt0 > 0) {
                    sum[y][1] += 0;
                    cnt0--;
                }
                else if(cnt1 > 0) {
                    sum[y][1] += 1;
                    cnt1--;
                }
                else {
                    sum[y][1] += 2;
                    cnt2--;
                }
            }
            
            for(int x = 2; x <= M; x++) {
                if(cnt0 > 0) {
                    sum[1][x] += 0;
                    cnt0--;
                }
                else if(cnt1 > 0) {
                    sum[1][x] += 1;
                    cnt1--;
                }
                else {
                    sum[1][x] += 2;
                    cnt2--;
                }
            }
        }
        
        for(int y = 2; y <= M; y++) {
            for(int x = 2; x <= M; x++)
                sum[y][x] += sum[y - 1][x];
        }
        
        for(int y = 1; y <= M; y++) {
            for(int x = 1; x <= M; x++)
                sb.append(sum[y][x] + 1).append(" ");
            sb.append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
}    //    Main-class-end

```