# 25_11_07_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1508 레이스

[1508번: 레이스](http://boj.ma/1508/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    레이스 트랙 길이
    private static int M;    //    배치할 심판 수
    private static int K;    //    심판 배치 가능한 위치 수
    
    private static int[] posArr;
    
    private static String answer = "0000000000000000000000000000000000000000000000000000";
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        posArr = new int[K];
        st = new StringTokenizer(br.readLine());
        for(int k = 0; k < K; k++)
            posArr[k] = Integer.parseInt(st.nextToken());
        
        parametricSearch();
        
        System.out.println(answer);
    }    //    main-end
    
    private static void parametricSearch() {
        int start = 1;                            //    가능한 최소 간격
        int end = posArr[K - 1] - posArr[0];      //    가능한 최대 간격
        
        while(start <= end) {
            int mid = (start + end) / 2;    //    심판관 사이의 최소 간격을 mid로 설정
            
            boolean[] left = new boolean[K];
            boolean[] right = new boolean[K];
            int leftCnt = 0;
            int rightCnt = 0;
            
            //    왼쪽부터 mid 간격으로 M명 채워보기
            int curPos = 0;
            left[curPos] = true;
            leftCnt = 1;
            
            for(int i = 1; i < K; i++) {
                if(posArr[i] - posArr[curPos] >= mid) {
                    leftCnt++;
                    left[i] = true;
                    if(leftCnt == M)
                        break;
                    curPos = i;
                }
            }
            
            curPos = K - 1;
            right[curPos] = true;
            rightCnt = 1;
            
            for(int i = K - 2; i >= 0; i--) {
                if(posArr[curPos] - posArr[i] >= mid) {
                    rightCnt++;
                    right[i] = true;
                    if(rightCnt == M)
                        break;
                    curPos = i;
                }
            }
            
            if(leftCnt < M && rightCnt < M)    //    최소 간격이 너무 커서 M명 다 배치 못할 경우
                end = mid - 1;                 //    최소 간격 좁히기
            else {                                            //    최소 간격이 M명 다 배치할 수 있을 정도로 좁을 경우
                StringBuilder sb = new StringBuilder();
                
                if(leftCnt >= M) {    //    왼쪽부터 M명 배치 가능할 경우
                    for(int i = 0; i < K; i++)
                        sb.append(left[i] ? "1" : "0");
                }
                else {    //    오른쪽부터 M명 배치 가능할 경우
                    for(int i = 0; i < K; i++)
                        sb.append(right[i] ? "1" : "0");
                }
                
                answer = sb.toString();
                
                start = mid + 1;               //    최소 간격 넓히기
            }
        }
    }
}    //    Main-class-end
```