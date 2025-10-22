# 25_10_22_daily_certification

# Problem Solving (Algorithm & SQL)

### 프로그래머스 2차원 동전 뒤집기

[](https://school.programmers.co.kr/learn/courses/30/lessons/131703)

```java
class Solution {
    private final int INF = Integer.MAX_VALUE;
    
    private int N;
    private int M;
    private int maxMask;
    
    private int[][] copied;
    
    private int answer = INF;
    
    public int solution(int[][] beginning, int[][] target) {
        N = beginning.length;
        M = beginning[0].length;
        maxMask = (1 << (N + M)) - 1;
        copied = new int[N][M];
        
        for(int mask = 0; mask <= maxMask; mask++) {
            copy(beginning);
            int bitstream = mask;
            int flipCnt = Integer.bitCount(mask);
            int idx = 0;
            
            while(idx < N + M) {
                int bit = bitstream & 1;
                
                if(bit == 1)
                    flip(idx >= N ? idx - N : idx, idx < N);
                
                idx++;
                bitstream >>= 1;
            }
            
            if(same(target))
                answer = Math.min(answer, flipCnt);
        }
        
        return answer == INF ? -1 : answer;
    }
    
    private boolean same(int[][] target) {
        for(int y = 0; y < N; y++) {
            for(int x = 0; x < M; x++) {
                if(copied[y][x] != target[y][x])
                    return false;
            }
        }
        
        return true;
    }
    
    private void copy(int[][] beginning) {
        for(int y = 0; y < N; y++) {
            for(int x = 0; x < M; x++)
                copied[y][x] = beginning[y][x];
        }
    }
    
    private void flip(int idx, boolean row) {
        if(row) {
            for(int x = 0; x < M; x++)
                copied[idx][x] = copied[idx][x] == 1 ? 0 : 1;
        }
        else {
            for(int y = 0; y < N; y++)
                copied[y][idx] = copied[y][idx] == 1 ? 0 : 1;
        }
    }
}
```