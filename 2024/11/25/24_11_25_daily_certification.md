# 24_11_25_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 17179**  케이크 자르기

[17179번: 케이크 자르기](https://boj.ma/17179/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;    //    자르는 횟수 N
    private static int M;    //    롤케이크 자를 수 있는 위치 M
    private static int L;    //    롤케이크 길이 L
    
    private static int[] cuttingPoints;    //    자를 수 있는 위치 (오름차순)
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        L = Integer.parseInt(st.nextToken());
        
        cuttingPoints = new int[M + 2];
        cuttingPoints[M + 1] = L;
        
        for(int i = 1; i <= M; i++)
            cuttingPoints[i] = Integer.parseInt(br.readLine());
        
        for(int q = 0; q < N; q++) {
            int cnt = Integer.parseInt(br.readLine());
            
            sb.append(parametricSearch(cnt)).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
    
    //    롤케이크를 cnt번 잘라야할때 가장 작은 조각의 최대 크기
    private static int parametricSearch(int cnt) {
        int start = 1;
        int end = L;
        int res = 0;
        
        while(start <= end) {
            int mid = (start + end) / 2;    //    롤케이크 한 조각 크기를 최소 mid 이상으로 잘라야 한다고 가정

            if(ok(mid, cnt)) {    //    mid 이상 크기로 cnt번 자를 수 있을 경우
                res = mid;          //    일단 mid 저장
                start = mid + 1;    //    더 큰 크기로 자를 수 있나 확인해보기
            }
            else    //    mid 이상 크기로 cnt번 자를 수 없을 경우
                end = mid - 1;    //    더 작은 크기로 잘라야 함
        }
        
        return res;
    }
    
    //    롤케이크 조각을 size 이상의 크기로 cnt번 자를 수 있으면 true
    private static boolean ok(int size, int cnt) {
        int cut = 0;    //    자를 횟수
        
        int idx = 1;    //    idx : 자르는 위치의 index
        int curSize = cuttingPoints[idx] - cuttingPoints[idx - 1];    //    현재 자르는 위치 기준 왼쪽 롤케이크 조각 크기
        int resSize = L - cuttingPoints[idx];
        
        while(idx <= M) {
            if(curSize >= size && resSize >= size) {    //    현재 조각과 남은 조각 모두 size 이상이면 자를 수 있음
                cut++;    //    자르는 횟수 1 증가
                
                if(cut == cnt)    //    cnt번 잘랐을 경우
                    return true;
                
                idx++;
                curSize = cuttingPoints[idx] - cuttingPoints[idx - 1];
                resSize = L - cuttingPoints[idx];
            }
            else if(resSize < size)    //    잘랐을때 남은 조각이 size 미만이면 자를 수 없음
                break;
            else if(curSize < size) {    //    현재 위치에서 자르는 것으로는 부족할 경우
                idx++;
                curSize += cuttingPoints[idx] - cuttingPoints[idx - 1];
                resSize = L - cuttingPoints[idx];
            }
        }
        
        return false;    //    cnt 이상 자를 수 없는 경우
    }
}    //    Main-class-end
```