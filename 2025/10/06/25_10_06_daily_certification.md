# 25_10_06_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 3089 **네잎 클로버를 찾아서**

[3089번: 네잎 클로버를 찾아서](http://boj.ma/3089/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;
import java.util.StringTokenizer;
import java.util.TreeSet;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;        //    네잎 클로버 수
    private static int M;        //    명령어 수
    private static char[] cmd;   //    명령어 리스트
    
    private static int cy = 0;
    private static int cx = 0;    //    현재 좌표
    
    //    Key : X 좌표 x, Value : X 좌표가 x인 네잎 클로버의 y 좌표 집합
    private static final Map<Integer, TreeSet<Integer>> xToYMap = new HashMap<>();
    //    Key : Y 좌표 y, Value : Y 좌표가 y인 네잎 클로버의 x 좌표 집합
    private static final Map<Integer, TreeSet<Integer>> yToXMap = new HashMap<>();
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        for(int i = 0; i < N; i++) {
            st = new StringTokenizer(br.readLine());
            int x = Integer.parseInt(st.nextToken());
            int y = Integer.parseInt(st.nextToken());
            
            TreeSet<Integer> ySet = xToYMap.getOrDefault(x, new TreeSet<>());
            TreeSet<Integer> xSet = yToXMap.getOrDefault(y, new TreeSet<>());
            
            ySet.add(y);
            xSet.add(x);
            
            xToYMap.put(x, ySet);
            yToXMap.put(y, xSet);
        }
        
        cmd = br.readLine().toCharArray();
        for(char dir : cmd) {
            if(dir == 'L' || dir == 'R') {    //    왼쪽 또는 오른쪽으로 이동할 경우
                TreeSet<Integer> xSet = yToXMap.get(cy);    //    현재 위치에서 수평 이동
                
                if(dir == 'L') {    //    cx보다 작으면서 가장 큰 위치 찾기
                    int nx = xSet.lower(cx);
                    cx = nx;
                }
                else {    //    cx보다 크면서 가장 작은 위치 찾기
                    int nx = xSet.higher(cx);
                    cx = nx;
                }
            }
            else {    //    위쪽 또는 아래쪽으로 이동할 경우
                TreeSet<Integer> ySet = xToYMap.get(cx);    //    현재 위치에서 수직 이동
                
                if(dir == 'D') {    //    cy보다 작으면서 가장 큰 위치 찾기
                    int ny = ySet.lower(cy);
                    cy = ny;
                }
                else {    //    cy보다 크면서 가장 작은 위치 찾기
                    int ny = ySet.higher(cy);
                    cy = ny;
                }
            }
        }
        
        System.out.println(cx + " " + cy);
    }    //    main-end
}    //    Main-class-end
```