# 25_08_30_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1022 소용돌이 예쁘게 출력하기

[1022번: 소용돌이 예쁘게 출력하기](http://boj.ma/1022/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int R1;
    private static int C1;
    private static int R2;
    private static int C2;
    
    private static int num = 1;
    private static int cy = 0;
    private static int cx = 0;
    
    private static String[][] snails;         //    달팽이 부분배열
    private static String longest = "";       //    달팽이 부분배열에 들어갈 수 중 가장 자릿수가 긴 수
    private static int max = 0;
    private static int filled = 0;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        R1 = Integer.parseInt(st.nextToken());
        C1 = Integer.parseInt(st.nextToken());
        R2 = Integer.parseInt(st.nextToken());
        C2 = Integer.parseInt(st.nextToken());
        
        snails = new String[R2 - R1 + 1][C2 - C1 + 1];
        max = (R2 - R1 + 1) * (C2 - C1 + 1);
        
        if(isIn(cy, cx)) {
            snails[cy - R1][cx - C1] = Integer.toString(num);
            longest = snails[cy - R1][cx - C1];
            filled++;
        }
        num++;
        
        if(filled < max) {
            OUTER:
            while(true) {
                for(int d = 0; d < 4; d++) {
                    int cnt = cnts[d];
                
                    for(int i = 0; i < cnt; i++) {
                        cy += dy[d];
                        cx += dx[d];
                    
                        if(isIn(cy, cx)) {
                            snails[cy - R1][cx - C1] = Integer.toString(num);
                            if(longest.length() < snails[cy - R1][cx - C1].length()) 
                                longest = snails[cy - R1][cx - C1];
                            
                            filled++;
                            if(filled == max)
                                break OUTER;
                        }
                        
                        num++;
                    }
                
                    cnts[d] += 2;
                }
            }
        }
        
        for(int y = 0; y <= R2 - R1; y++) {
            for(int x = 0; x <= C2 - C1; x++) {
                String num = snails[y][x];
                int paddCnt = longest.length() - num.length();
                for(int i = 0; i < paddCnt; i++)
                    num = " " + num;
                    
                sb.append(num);
                
                if(x != C2 - C1)
                    sb.append(" ");
            }
            
            if(y < R2 - R1)
                sb.append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static final int[] dy = {0, -1, 0, 1};
    private static final int[] dx = {1, 0, -1, 0};
    private static final int[] cnts = {1, 1, 2, 2};
    
    private static boolean isIn(int y, int x) {
        return (R1 <= y && y <= R2) && (C1 <= x && x <= C2); 
    }
}    //    Main-class-end
```