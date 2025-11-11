# 25_11_11_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 15732 도토리 숨기기

[15732번: 도토리 숨기기](http://boj.ma/15732/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    상자 개수
    private static int K;    //    규칙 개수
    private static int D;    //    도토리 개수
    
    private static Rule[] rules;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        D = Integer.parseInt(st.nextToken());
        
        rules = new Rule[K];
        for(int k = 0; k < K; k++) {
            st = new StringTokenizer(br.readLine());
            rules[k] = new Rule(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken()));
        }
        
        System.out.println(parametricSearch());
    }    //    main-end
    
    //    규칙대로 도토리를 앞에서부터 넣었을때 D번째 도토리가 들어가는 상자 번호
    private static int parametricSearch() {
        int start = 1;
        int end = N;
        int res = N;
        
        while(start <= end) {
            int mid = (start + end) / 2;
            
            if(ok(mid)) {    //    1 ~ mid의 상자에 D개의 도토리를 모두 넣을 수 있을 경우
                res = mid;
                end = mid - 1;
            }
            else    //    1 ~ mid의 상자에 D개의 도토리를 모두 넣기엔 부족할 경우
                start = mid + 1;
        }
        
        
        return res;
    }
    
    //    K개의 규칙대로 1 ~ limit의 상자에 도토리를 넣을 때 D개 이상의 도토리를 넣을 수 있으면 true
    private static boolean ok(int limit) {
        long cnt = 0;
        
        for(Rule rule : rules) {
            int start = rule.start;
            int end = rule.end;
            int gap = rule.gap;
            
            if(start > limit)
                continue;
            
            cnt++;    //    start에 1개
            end = Math.min(end, limit);
            cnt += (end - start) / gap;
        }
        
        return cnt >= D;
    }
    
    private static class Rule {
        public int start;
        public int end;
        public int gap;
        
        public Rule(int start, int end, int gap) {
            this.start = start;
            this.end = end;
            this.gap = gap;
        }
    }
}    //    Main-class-end
```