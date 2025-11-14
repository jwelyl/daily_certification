# 25_11_14_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 30023 전구 상태 바꾸기

[30023번: 전구 상태 바꾸기](http://boj.ma/30023/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;

public class Main {
    private static final int NONE = Integer.MAX_VALUE;
    
    private static final int R = 0;
    private static final int G = 1;
    private static final int B = 2;
    
    private static final int LEN = 3;    //    한번에 연속으로 바꿔야 하는 전구 개수
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static int N;
    private static char[] input;
    private static int[] bulbs;
    
    private static int answer = NONE;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        input = br.readLine().toCharArray();
        bulbs = new int[N];
        
        //    color로 일치시킬 수 있나 확인
        for(int color = R; color <= B; color++) {
            int total = 0;
            copy();
            
            for(int i = 0; i <= N - LEN; i++) {
                if(bulbs[i] != color) {
                    int cnt = (color - bulbs[i] + 3) % 3;
                    for(int j = i; j < i + LEN; j++)
                        flip(j, cnt);
                    
                    total += cnt;
                }
            }
            
            for(int i = N - LEN + 1; i < N; i++) {
                if(bulbs[i] != color) {
                    total = NONE;
                    break;
                }
            }
            
            answer = Math.min(answer, total);
        }
        
        System.out.println(answer == NONE ? -1 : answer);
    }    //    main-end
    
    private static void copy() {
        for(int i = 0; i < N; i++)
            bulbs[i] = input[i] == 'R' ? R : (input[i] == 'G' ? G : B);
    }
    
    private static void flip(int idx, int cnt) {
        bulbs[idx] = (bulbs[idx] + cnt) % 3;
    }
}    //    Main-class-end
```