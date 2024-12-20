# 24_12_20_daily_certification

### **BOJ** 25978 **2차원 배열 다중 업데이트 다중 합**

[25978번: 2차원 배열 다중 업데이트 다중 합](https://boj.ma/25978/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;    //    배열 크기 N * N
    private static int M;    //    질의 개수
    private static long[][] arr;    //    원본 배열 -> 최종적으로 누적합 배열
    private static long[][] add;    //    질의 1로 (i1, j1), (i2, j2) 영역에 추가되는 것을 반영한 배열
    
    private static boolean isEnd = false;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        arr = new long[N + 2][N + 2];
        add = new long[N + 2][N + 2];
        
        for(int i = 1; i <= N; i++) {
            st = new StringTokenizer(br.readLine());
            for(int j = 1; j <= N; j++)
                arr[i][j] = Integer.parseInt(st.nextToken());
        }
        
        for(int q = 0; q < M; q++) {
            st = new StringTokenizer(br.readLine());
            int query = Integer.parseInt(st.nextToken());
            
            if(query == 1) {
                int i1 = Integer.parseInt(st.nextToken()) + 1;
                int j1 = Integer.parseInt(st.nextToken()) + 1;
                int i2 = Integer.parseInt(st.nextToken()) + 1;
                int j2 = Integer.parseInt(st.nextToken()) + 1;
                int k = Integer.parseInt(st.nextToken());
                
                add[i1][j1] += k;
                add[i1][j2 + 1] -= k;
                add[i2 + 1][j1] -= k;
                add[i2 + 1][j2 + 1] += k;
            }
            else {
                if(!isEnd) {   //    질의 1이 이제 끝난 경우, 더 이상 갱신될 일 없음, 누적합 계산
                    isEnd = true;
                    calc();
                }
                
                int i1 = Integer.parseInt(st.nextToken()) + 1;
                int j1 = Integer.parseInt(st.nextToken()) + 1;
                int i2 = Integer.parseInt(st.nextToken()) + 1;
                int j2 = Integer.parseInt(st.nextToken()) + 1;
                
                sb.append(partialSum(i1, j1, i2, j2)).append("\n");        
            }
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static void calc() {
        for(int i = 1; i <= N; i++) {
            for(int j = 1; j <= N; j++) {
                add[i][j] = add[i][j - 1] + add[i - 1][j] - add[i - 1][j - 1] + add[i][j];
                arr[i][j] += add[i][j];
                arr[i][j] = arr[i][j - 1] + arr[i - 1][j] - arr[i - 1][j - 1] + arr[i][j];
            }
        }
    }
    
    private static long partialSum(int i1, int j1, int i2, int j2) {
        return arr[i2][j2] - arr[i2][j1 - 1] - arr[i1 - 1][j2] + arr[i1 - 1][j1 - 1];
    }
}    //    Main-class-end
```