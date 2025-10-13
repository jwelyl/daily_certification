# 25_10_13_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 24492 **Cow Frisbee**

[24492번: Cow Frisbee](http://boj.ma/24492/t)

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