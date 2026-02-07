# 26_02_07_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1891 사분면

[1891번: 사분면](http://boj.ma/1891/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int D;            //    자릿수
    private static String number;    //    조각 번호
    
    private static long[] pos;
    private static long py;
    private static long px;          //    해당 조각의 좌표평면 상 위치
    
    private static long sy = 0;
    private static long sx = 0;      //    조각 좌표 시작점
    private static long ey = 0;
    private static long ex = 0;      //    조각 좌표 끝점
    
    private static long my = 0;
    private static long mx = 0;      //    이동 칸 수
    
    private static String answer = null;
        
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        D = Integer.parseInt(st.nextToken());
        number = st.nextToken();
        
        st = new StringTokenizer(br.readLine());
        mx = Long.parseLong(st.nextToken());
        my = -Long.parseLong(st.nextToken());    //    위쪽일 경우 음수로 변경, 아래쪽일 경우 양수로 변경
        
        ey = (long)Math.pow(2, D) - 1;
        ex = (long)Math.pow(2, D) - 1;
        
        pos = recursion(D, sy, sx, ey, ex);
        
        //  System.out.println("[" + pos[0] + " " + pos[1] + "]");
        
        if(isIn(pos[0] + my, pos[1] + mx)) {
            pos[0] += my;
            pos[1] += mx;
            recursion(sy, sx, ey, ex, new StringBuilder());
            System.out.println(answer);
        }
        else
            System.out.println(-1);    //    존재하지 않는 사분면
    }    //    main-end
    
    private static void recursion(long sy, long sx, long ey, long ex, StringBuilder sb) {
        if(sy == ey && sx == ex) {    //    (py, px)가 해당되는 사분면 번호를 찾은 경우
            answer = sb.toString();
            return;
        }
        
        long my = (sy + ey) / 2; 
        long mx = (sx + ex) / 2;
        
        //    1, 2 사분면일 경우
        if(sy <= pos[0] && pos[0] <= my) {
            if(mx + 1 <= pos[1]) {    //    1 사분면일 경우
                sb.append(1);
                recursion(sy, mx + 1, my, ex, sb);
            }
            else {                    //    2 사분면일 경우
                sb.append(2);
                recursion(sy, sx, my, mx, sb);
            }
        }
        //    3, 4 사분면일 경우
        else {
            if(pos[1] <= mx) {    //    3 사분면일 경우
                sb.append(3);
                recursion(my + 1, sx, ey, mx, sb);
            }
            else {                //    4 사분면일 경우
                sb.append(4);
                recursion(my + 1, mx + 1, ey, ex, sb);
            }
        }
    }
    
    private static long[] recursion(int d, long sy, long sx, long ey, long ex) {
        long[] res = new long[2];
        
        int digit = number.charAt(D - d) - '0';
        
        if(d == 1) {
            if(1 <= digit && digit <= 2) {
                res[0] = sy;
                res[1] = digit == 1 ? ex : sx;
            }
            else {
                res[0] = ey;
                res[1] = digit == 4 ? ex : sx;
            }
            
            return res;
        }
        
        long my = (sy + ey) / 2;
        long mx = (sx + ex) / 2;
        
        if(digit == 1)
            return recursion(d - 1, sy, mx + 1, my, ex);
        else if(digit == 2)
            return recursion(d - 1, sy, sx, my, mx);
        else if(digit == 3)
            return recursion(d - 1, my + 1, sx, ey, mx);
        else
            return recursion(d - 1, my + 1, mx + 1, ey, ex);
    }

    private static boolean isIn(long y, long x) {
        return (sy <= y && y <= ey) && (sx <= x && x <= ex); 
    }
}    //    Main-class-end
```