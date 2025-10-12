# 25_10_12_daily_certification

# To Do List

```
[#285 koreii] 데일리인증 20251012
1. 서류 작성
- 서류 3 작성 후 제출
2. 알고리즘 문제 풀이
- BOJ 7662 이중 우선순위 큐 (TreeMap)
```

```
[#104] 데일리인증 20251012 (285 / 365)
1. 서류 작성
- 서류 3 작성 후 제출
2. 알고리즘 문제 풀이
- BOJ 7662 이중 우선순위 큐 (TreeMap)
```

# Problem Solving (Algorithm & SQL)

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