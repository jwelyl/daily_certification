# 25_03_26_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 14595 동방 프로젝트

[14595번: 동방 프로젝트 (Large)](http://boj.ma/14595/t)

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
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    
    private static final List<Query> queries = new ArrayList<>();
    
    private static int answer = 0;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        M = Integer.parseInt(br.readLine());
        
        answer = N;
        
        for(int q = 0; q < M; q++) {
            st = new StringTokenizer(br.readLine());
            queries.add(new Query(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken())));
        }
        
        Collections.sort(queries);
        
        int lastEnd = 0;
        
        for(Query query : queries) {
            int from = query.from;
            int to = query.to;
            
            if(lastEnd < from)    //    기존에 합쳐진 방 구간과 겹치지 않을 경우
                answer -= (to - from);
            else
                answer -= Math.max((to - lastEnd), 0);
            
            lastEnd = Math.max(lastEnd, to);
        }
        
        System.out.println(answer);
    }    //    main-end
                        
    private static class Query implements Comparable<Query> {
        public int from;
        public int to;
        
        public Query(int from, int to) {
            this.from = from;
            this.to = to;
        }
        
        @Override
        public int compareTo(Query other) {
            int res = Integer.compare(this.from, other.from);
            
            if(res == 0)
                res = Integer.compare(this.to, other.to);
            
            return res;
        }
    }
}    //    Main-class-end

```