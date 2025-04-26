# 25_04_25_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2819 상근이의 로봇

[2819번: 상근이의 로봇](http://boj.ma/2819/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Collections;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;    //    조사점 개수
    
    private static char[] cmd;	//	명령어
    
    //    조사점들을 x 좌표에 대해 오름차순으로 정렬한 리스트
    private static final List<int[]> xOrderedPosList = new ArrayList<>();
    //    조사점들을 y 좌표에 대해 오름차순으로 정렬한 리스트
    private static final List<int[]> yOrderedPosList = new ArrayList<>();

    private static int curX = 0;
    private static int curY = 0;	//	로봇의 좌표
    
    private static long prefixSum = 0;    //    로봇의 좌표와 모든 점들과의 맨허튼 거리 합
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        Integer.parseInt(st.nextToken());
        
        for(int i = 0; i < N; i++) {
            st = new StringTokenizer(br.readLine());
            int x = Integer.parseInt(st.nextToken());
            int y = Integer.parseInt(st.nextToken());
            int[] pos = {x, y};
            
            xOrderedPosList.add(pos);
            yOrderedPosList.add(pos);
            
            prefixSum += (Math.abs(curX - x) + Math.abs(curY - y));
        }
        
        Collections.sort(xOrderedPosList, (p1, p2) -> Integer.compare(p1[0], p2[0]));
        Collections.sort(yOrderedPosList, (p1, p2) -> Integer.compare(p1[1], p2[1]));

        cmd = br.readLine().toCharArray();
        
        for(char dir : cmd) {
            int idx = 0;
            int cnt1 = 0;    //    가까워지는 점 개수
            int cnt2 = 0;    //    멀어지는 점 개수
            
            switch(dir) {
                case 'S':    //    위로 이동
                    idx = binarySearch(1, yOrderedPosList, curY, true);    //    curY보다 y좌표가 크면서 y좌표가 최소인 조사점의 idx
                    
                    cnt1 = N - idx;    //    curY보다 y좌표가 큰 조사점 개수
                    cnt2 = N - cnt1;   //    curY보다 y좌표가 크지 않은 조사점 개수
                    
                    curY++;
                    break;
                case 'J':    //    아래로 이동
                    idx = binarySearch(1, yOrderedPosList, curY, false);    //    curY보다 y좌표가 작으면서 y좌표가 최대인 조사점의 idx
                    
                    cnt1 = idx + 1;    //    curY보다 y좌표가 작은 조사점 개수
                    cnt2 = N - cnt1;   //    curY보다 y좌표가 작지 않은 조사점 개수
                    
                    curY--;
                    break;
                case 'I':    //    오르쪽으로 이동
                    idx = binarySearch(0, xOrderedPosList, curX, true);
                    
                    cnt1 = N - idx;    //    curX보다 X좌표가 큰 조사점 개수
                    cnt2 = N - cnt1;   //    curX보다 X좌표가 크지 않은 조사점 개수
                    
                    curX++;
                    break;
                case 'Z':    //    왼쪽으로 이동
                    idx = binarySearch(0, xOrderedPosList, curX, false);
                    
                    cnt1 = idx + 1;    //    curX보다 x좌표가 작은 조사점 개수
                    cnt2 = N - cnt1;   //    curX보다 x좌표가 작지 않은 조사점 개수
                    
                    curX--;
                    break;
            }
            
            prefixSum -= cnt1;    //    거리가 1씩 가까워지는 점들만큼 합에서 뺀다. 
            prefixSum += cnt2;    //    거리가 1만큼 멀어지는 점들만큼 합에 더한다. 
            
            sb.append(prefixSum).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
    
    //    upperBound = true일 경우 orderdPosList[res][idx]가 cur보다 크면서 최소인 res를 찾음
    //    upperBound = false인 경우 orderdPosList[res][idx]가 cur보다 작으면서 최대인 res를 찾음
    private static int binarySearch(int idx, List<int[]> orderedPosList, int cur, boolean upperBound) {
        int start = 0;
        int end = N - 1;
        //    upperBound = true일때, cur보다 큰 좌표가 없을 경우 M
        //    upperBound = false일때, cur보다 작은 좌표가 없을 경우 -1
        int res = upperBound ? N : -1;
            
        while(start <= end) {
            int mid = (start + end) / 2;
            
            int[] pos = orderedPosList.get(mid);
            
            if(upperBound) {    //    cur보다 크면서 최소를 찾아야 함
                if(cur >= pos[idx])    //    cur보다 작거나 같을 경우
                    start = mid + 1;   //    더 오른쪽에서 찾아야 함
                else {    //    cur보다 더 클 경우
                    res = mid;         //    일단 저장
                    end = mid - 1;     //    더 왼쪽에서도 찾아보기
                } 
            }
            else {
                if(cur <= pos[idx])    //    cur보다 크거나 같을 경우
                    end = mid - 1;   //    더 왼쪽에서 찾아야 함
                else {    //    cur보다 더 작을 경우
                    res = mid;         //    일단 저장
                    start = mid + 1;   //    더 오른쪽에서도 찾아보기
                } 
            }
        }
        
        return res;
    }
}    //    Main-class-end
```