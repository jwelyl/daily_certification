# 25_10_20_daily_certification

# Problem Solving (Algorithm & SQL)

### **2개 이하로 다른 비트**

[](https://school.programmers.co.kr/learn/courses/30/lessons/77885)

```java
import java.util.Arrays;

class Solution {
    private final int MAX = 63;
    public long[] solution(long[] numbers) {
        long[] answer = new long[numbers.length];
        
        for(int i = 0; i < numbers.length; i++) {
            long cur = numbers[i];
            long next = cur + 1;
            int diff = Long.bitCount(cur ^ next);   //  cur과 next의 서로 다른 비트 수
            
            if(diff <= 2)
                answer[i] = cur + 1;
            else {
                char[] binary = decToBin(cur + 1);
                int idx = MAX - 1;
                
                for(int j = 0; j < diff - 2; j++)
                    binary[idx--] = '1';
                
                answer[i] = binToDec(binary);
            }
        }
        
        return answer;
    }
    
    public long binToDec(char[] binary) {
        long res = 0;
        long mult = 1;
        
        for(int i = MAX - 1; i >= 0; i--) {
            res += (binary[i] - '0') * mult;
            mult *= 2;
        }
        
        return res;
    }
    
    public char[] decToBin(long decimal) {
        char[] res = new char[63];
        String binStr = Long.toBinaryString(decimal);
        int idx = MAX - 1;
        
        Arrays.fill(res, '0');
        
        for(int i = binStr.length() - 1; i >= 0; i--)
            res[idx--] = binStr.charAt(i);
        
        return res;
    }
}
```

### 고고학 최고의 발견

[](https://school.programmers.co.kr/learn/courses/30/lessons/131702)

```java
class Solution {
    //  ROT1[status] : status 상태에서 0으로 되기 위해 회전시켜야 하는 횟수
    private final int[] ROT1 = {0, 3, 2, 1};
    
    //  ROT2[status][rot] : status 상태에서 rot번 회전시켰을때 결과
    private final int[][] ROT2 = {
        {0, 1, 2, 3},
        {1, 2, 3, 0},
        {2, 3, 0, 1},
        {3, 0, 1, 2}
    };
    
    private int N;
    private int[][] orig;
    private int[][] copied;
    
    private int answer = Integer.MAX_VALUE;
    
    public int solution(int[][] clockHands) {
        N = clockHands.length;
        orig = clockHands;
        copied = new int[N][N];
        
        permutation(0, new int[N]);
        
        return answer;
    }
    
    //  cnt[col] : 0행 col열을 회전시킨 횟수
    private void permutation(int nth, int[] cnt) {
        if(nth == N) {
            copy();
            
            int res = 0;
            
            //  정해진 횟수만큼 0행의 모든 열을 회전시킴
            for(int col = 0; col < N; col++) {
                res += cnt[col];
                rotate(0, col, cnt[col]);
            }
            
            for(int row = 1; row < N; row++) {
                for(int col = 0; col < N; col++) {
                    if(copied[row - 1][col] != 0) {
                        int req = ROT1[copied[row - 1][col]];
                        
                        res += req;
                        rotate(row, col, req);
                    }
                }
            }
            
            if(isValid())
                answer = Math.min(answer, res);
            return;
        }
        
        for(int rot = 0; rot < 4; rot++) {
            cnt[nth] = rot;
            permutation(nth + 1, cnt);
        }
    }
    
    //  (y, x)칸을 rot번 회전시킴
    private void rotate(int y, int x, int rot) {
        for(int d = 0; d < 5; d++) {
            int ny = y + dy[d];
            int nx = x + dx[d];
            
            if(isIn(ny, nx))
                copied[ny][nx] = ROT2[copied[ny][nx]][rot];
        }
    }
    
    //  회전시킨 결과, 행렬의 모든 값이 0이 된 경우
    private boolean isValid() {
        for(int y = 0; y < N; y++) {
            for(int x = 0; x < N; x++)
                if(copied[y][x] != 0)
                    return false;
        }
        
        return true;
    }
    
    private void copy() {
        for(int y = 0; y < N; y++)
            for(int x = 0; x < N; x++)
                copied[y][x] = orig[y][x];
    }
    
    private final int[] dy = {0, 0, 1, 0, -1};
    private final int[] dx = {0, 1, 0, -1, 0};
    
    private boolean isIn(int y, int x) {
        return (0 <= y && y < N) && (0 <= x && x < N);
    }
}
```