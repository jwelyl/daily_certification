# 25_03_21_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2015 수들의 합 4

[2015번: 수들의 합 4](http://boj.ma/2015/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.Map;
import java.util.HashMap;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    //    Key : prefixSum[i], Value : prefixSum[i] 개수
    private static final Map<Long, Integer> map = new HashMap<>();
    
    private static int N;
    private static int K;
    private static long[] prefixSum;
    
    private static long answer = 0;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        map.put(0L, 1);
        
        prefixSum = new long[N + 1];
        st = new StringTokenizer(br.readLine());
        for(int i = 1; i <= N; i++) {
            prefixSum[i] = prefixSum[i - 1] + Integer.parseInt(st.nextToken());
            
            long target = prefixSum[i] - K;
            int cnt = map.getOrDefault(target, 0);
            
            answer += cnt;
            map.put(prefixSum[i], map.getOrDefault(prefixSum[i], 0) + 1);
        }
        
        System.out.println(answer);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 7662 이중 우선순위 큐

[7662번: 이중 우선순위 큐](http://boj.ma/7662/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.TreeMap;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int T;
    private static int K;
    
    private static final TreeMap<Integer, Integer> dualPq = new TreeMap<>();
    
    public static void main(String[] args) throws IOException {
        T = Integer.parseInt(br.readLine());
        
        for(int tc = 1; tc <= T; tc++) {
            dualPq.clear();
            
            K = Integer.parseInt(br.readLine());
            
            for(int k = 0; k < K; k++) {
                st = new StringTokenizer(br.readLine());
                
                char opt = st.nextToken().charAt(0);
                int num = Integer.parseInt(st.nextToken());
                
                if(opt == 'I')
                	dualPq.put(num, dualPq.getOrDefault(num, 0) + 1);
                else {
                	if(dualPq.isEmpty())
                        continue;
                	
                	int out = num == -1 ? dualPq.firstKey() : dualPq.lastKey();
                	int cnt = dualPq.get(out);
                	
                	if(cnt == 1)
                		dualPq.remove(out);
                	else
                		dualPq.put(out, cnt - 1);
                }
            }
            
            if(dualPq.isEmpty())
            	sb.append("EMPTY\n");
            else
            	sb.append(dualPq.lastKey()).append(" ").append(dualPq.firstKey()).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 1107 리모컨

[1107번: 리모컨](http://boj.ma/1107/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
    private static final int MAX = 500_000;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    private static final boolean[] disabled = new boolean[10];
    
    private static int answer = 0;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        M = Integer.parseInt(br.readLine());
        answer = Math.abs(N - 100);    //    숫자 버튼 없이 상하키만으로 이동하는 횟수
        
        if(M > 0) {
            st = new StringTokenizer(br.readLine());
            for(int i = 0; i < M; i++)
                disabled[Integer.parseInt(st.nextToken())] = true;
        }
        
        for(int channel = 0; channel <= MAX * 2; channel++) {
            String str = Integer.toString(channel);
            
            boolean canGo = true;
            for(int i = 0; i < str.length(); i++) {
                int digit = str.charAt(i) - '0';
                
                if(disabled[digit]) {
                    canGo = false;
                    break;
                }
            }
            
            int dist = 0;
            if(canGo) {    //    숫자 버튼으로 channel로 이동할 수 있을 경우
                dist = str.length();                //    숫자 버튼 눌러서 channel로 이동하는 횟수
                dist += Math.abs(N - channel);      //    채널 이동 후 상하키로 N까지 이동하는 횟수 추가
            
                answer = Math.min(answer, dist);
            }
        }
        
        System.out.println(answer);
    }    //    main-end
}    //    Main-class-end
```