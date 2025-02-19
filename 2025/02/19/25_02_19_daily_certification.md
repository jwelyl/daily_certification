# 25_02_19_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2229 조 짜기

[2229번: 조 짜기](http://boj.ma/2229/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st; 
   
    private static int N;
    private static int[] scores;
    
    private static int[][] maxScores;    //    maxScores[i][j] : [i, j] 학생 중 최대 점수
    private static int[][] minScores;    //    minScores[i][j] : [i, j] 학생 중 최소 점수
    
    private static int[] dp;    //    dp[i] :[0, i] 학생까지 조 편성했을때 조 점수 합의 최댓값
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        scores = new int[N];
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++)
            scores[i] = Integer.parseInt(st.nextToken());
        
        maxScores = new int[N][N];
        minScores = new int[N][N];
        dp = new int[N];
        
        for(int y = 0; y < N; y++) {
            for(int x = y; x < N; x++) {
                if(y == x) {
                    maxScores[y][x] = scores[y];
                    minScores[y][x] = scores[y];
                }
                else {
                    maxScores[y][x] = Math.max(maxScores[y][x - 1], scores[x]);
                    minScores[y][x] = Math.min(minScores[y][x - 1], scores[x]);
                }
            }
        }
        
        for(int i = 1; i < N; i++) {
            dp[i] = maxScores[0][i] - minScores[0][i];    //    [0, i]를 전부 한 팀으로 할때 점수
            
            for(int j = 0; j < i; j++)
                //    [0, j]까지 팀원들을 조편성 했을때 최댓값 + [j + 1, i]를 전부 한 팀으로 할때의 점수
                dp[i] = Math.max(dp[i], dp[j] + maxScores[j + 1][i] - minScores[j + 1][i]);
        }
        
        System.out.println(dp[N - 1]);
    }    //    main-end
}    //    Main-class-end
```