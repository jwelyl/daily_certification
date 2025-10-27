# 25_10_27_daily_certification

# Problem Solving (Algorithm & SQL)

### 프로그래머스 기지국 설치

[](https://school.programmers.co.kr/learn/courses/30/lessons/12979?language=java#)

```java
import java.util.List;
import java.util.ArrayList;

class Solution {
    //  전파가 닿지 않는 구간
    private final List<int[]> segments = new ArrayList<>();
    
    private int start = 1;
    
    public int solution(int n, int[] stations, int w) {
        int cover = 2 * w + 1;
        int answer = 0;

        for(int pos : stations) {
            int left = Math.max(pos - w, 1);
            int right = Math.min(pos + w, n);
            
            if(start < left)
                segments.add(new int[] {start, left - 1});

            start = right + 1;
        }
        
        if(start <= n)
            segments.add(new int[] {start, n});
        
        for(int i = 0; i < segments.size(); i++) {
            int[] segment = segments.get(i);
            int len = segment[1] - segment[0] + 1;
            
            if(len <= 0)
                continue;
            
            int q = len / cover;
            int r = len % cover;
            
            answer += (q + (r == 0 ? 0 : 1));    
        }
        
        return answer;
    }
}
```