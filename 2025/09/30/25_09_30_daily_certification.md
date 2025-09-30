# 25_09_30_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 21942 부품 대여장

[21942번: 부품 대여장](http://boj.ma/21942/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Map;
import java.util.HashMap;
import java.util.TreeMap;

public class Main {
    private static final int DAY = 24 * 60;    //    일수를 분으로 변경하기 위해 곱할 값
    private static final int HOUR = 60;        //    시간을 분으로 변경하기 위해 곱할 갑
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;        //    대여기간 정보 개수
    private static String term;  //    대여기간
    private static int L;        //    허용 대여기간 (분)
    private static int F;        //    대여기간 초과 시 1분 당 벌금
    
    //    Key : 대여 부품명@대여한 사람
    //    Value : 대여 시각  (분)
    private static final Map<String, Integer> logs = new HashMap<>();
    //    Key : 늦은 사람 이름
    //    Value : 해당 사람이 내야 하는 벌금
    private static final Map<String, Long> lateMap = new TreeMap<>();
    
    //    MONTHS[i] : 2021 기준 1월부터 i월까지 누적 날의 수
    private static final int[] MONTHS = {0, 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};
    
    public static void main(String[] args) throws IOException {
        for(int i = 1; i <= 12; i++)
            MONTHS[i] = MONTHS[i - 1] + MONTHS[i];
        
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        term = st.nextToken();
        F = Integer.parseInt(st.nextToken());
        L = Integer.parseInt(term.substring(0, 3)) * DAY + Integer.parseInt(term.substring(4, 6)) * HOUR + Integer.parseInt(term.substring(7, 9));
    
        for(int i = 0; i < N; i++) {
            st = new StringTokenizer(br.readLine());
            String date = st.nextToken();
            int month = Integer.parseInt(date.substring(5, 7));
            int day = Integer.parseInt(date.substring(8, 10));
            String time = st.nextToken();
            int hour = Integer.parseInt(time.substring(0, 2));
            int minute = Integer.parseInt(time.substring(3, 5));
            String device = st.nextToken();    //    대여 또는 반납할 장비
            String member = st.nextToken();    //    대여 또는 반납하는 회원
            String key = device + "@" + member;
            int timestamp = (MONTHS[month - 1] + day) * DAY + hour * HOUR + minute;    //    대여 또는 반납하는 시각
            
            if(logs.containsKey(key)) {    //    반납일 경우
                int dueTime = logs.get(key) + L;    //    반납 기한
                
                if(timestamp > dueTime) {    //    반납 기한보다 늦게 반납할 경우
                    int fee = (timestamp - dueTime) * F;                    
                    lateMap.put(member, lateMap.getOrDefault(member, 0L) + fee);
                }
                
                logs.remove(key);
            }
            else    //    대여일 경우
                logs.put(key, timestamp);
        }
        
        if(lateMap.isEmpty())
            sb.append("-1\n");
        else {
            for(Map.Entry<String, Long> entry : lateMap.entrySet())
                sb.append(entry.getKey()).append(" ").append(entry.getValue()).append("\n");
        }
             
        System.out.print(sb);
    }    //    main-end
}    //    Main-class-end
```