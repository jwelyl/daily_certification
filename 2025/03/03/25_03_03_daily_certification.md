# 25_03_03_daily_certification

# Problem Solving (Algorithm & SQL)

### 프로그래머스 코딩 테스트 공부

[](https://school.programmers.co.kr/learn/courses/30/lessons/118668)

```java
import java.util.Arrays;

class Solution {
    private final int INF = 150 * 150 * 100 + 1;
    
    private int MAX_ALP = 0;    //  요구 알고력 중 가장 높은 알고력
    private int MAX_COP = 0;    //  요구 코딩력 중 가장 높은 코딩력
    
    //  dp[y][x] : 최초 알고력 alp, 최초 코딩력 cop로부터 알고력 y, 코딩력 x를 달성하기 위해 필요한 최소 시간
    private int[][] dp;
    
    public int solution(int alp, int cop, int[][] problems) {
        int answer = 0;
        
        MAX_ALP = alp;
        MAX_COP = cop;
        
        for(int[] problem : problems) {
            MAX_ALP = Math.max(MAX_ALP, problem[0]);
            MAX_COP = Math.max(MAX_COP, problem[1]);
        }
        
        dp = new int[MAX_ALP + 1][MAX_COP + 1];
        for(int y = 0; y <= MAX_ALP; y++)
            Arrays.fill(dp[y], INF);
        
        for(int y = alp; y <= MAX_ALP; y++) {
            for(int x = cop; x <= MAX_COP; x++) {
                dp[y][x] = Math.min(dp[y][x], (y - alp) + (x - cop));   //  부족한 알고력과 코딩력을 공부만으로 보충하는 시간
                
                for(int[] problem : problems) {
                    int reqAlp = problem[0];
                    int reqCop = problem[1];    //  problem을 풀기 위해 필요한 알고력, 코딩력
                    int rwdAlp = problem[2];
                    int rwdCop = problem[3];    //  problem을 풀었을때 얻을 수 있는 알고력, 코딩력
                    int cost = problem[4];      //  problem을 풀기 위해 필요한 시간
                    
                    if(reqAlp > y || reqCop > x)    //  현재 알고력과 코딩력으로는 problem을 풀 수 없는 경우
                        continue;
                    
                    int afterAlp = Math.min(y + rwdAlp, MAX_ALP);
                    int afterCop = Math.min(x + rwdCop, MAX_COP);   //  problem을 풀었다고 가정했을때 푼 이후 알고력과 코딩력
                    dp[afterAlp][afterCop] = Math.min(dp[afterAlp][afterCop], dp[y][x] + cost);
                }
            }
        }
        
        return dp[MAX_ALP][MAX_COP];
    }
}
```