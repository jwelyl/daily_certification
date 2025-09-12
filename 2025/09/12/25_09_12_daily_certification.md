# 25_09_12_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 6493 Slurpys

[6493번: Slurpys](http://boj.ma/6493/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.regex.Pattern;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    
    private static int T;
    
    public static void main(String[] args) throws IOException {
    	T = Integer.parseInt(br.readLine());
    	
    	for(int tc = 1; tc <= T; tc++) {
    		String input = br.readLine();
    		boolean isSluppy = false;
    		
    		if(input.length() >= 5) {
    			for(int i = 2; i <= input.length() - 3; i++) {
    				if(isSlimp(input.substring(0, i)) && isSlump(input.substring(i))) {
    					isSluppy = true;
    					break;
    				}
    			}
    		}
    		
    		sb.append(isSluppy ? "YES\n" : "NO\n");
    	}
    	
    	System.out.print(sb);
    }    //    main-end
    
    private static boolean isSlimp(String str) {
    	int len = str.length();
    	
    	if(len >= 2) {
        	if(str.charAt(0) != 'A')
        		return false;
    		
    		if(len == 2)
    			return str.charAt(1) == 'H';
    	
    		if(str.charAt(len - 1) != 'C')
    			return false;
    		
    		return (str.charAt(1) == 'B' && isSlimp(str.substring(2, len - 1))) || isSlump(str.substring(1, len - 1)); 
    	}
    	
    	return false;
    }
    
    private static boolean isSlump(String str) {
    	Pattern slump = Pattern.compile("((D|E)F+)+G");
    	return slump.matcher(str).matches();
    }
}    //    Main-class-end
```

### 프로그래머스 주사위 고르기

[](https://school.programmers.co.kr/learn/courses/30/lessons/258709)

```java
class Solution {
    private int N;  //  주사위 개수
    private int[][] DICE;
    private static int maxWin = 0;
    private static int[] answer;
    
    public int[] solution(int[][] dice) {
        N = dice.length;
        DICE = dice;
        
        answer = new int[N / 2];
        
        combination(0, 0, new boolean[N]);
        
        return answer;
    }
    
    private void combination(int nth, int start, boolean[] selected) {
        if(nth == N / 2) {  //  A가 가질 주사위 N / 2개, B가 가질 주사위 N / 2개 정해진 경우
            int[] aDices = new int[N / 2];
            int[] bDices = new int[N / 2];
            int aIdx = 0;
            int bIdx = 0;
            
            for(int i = 0; i < N; i++) {
                if(selected[i])
                    aDices[aIdx++] = i;
                else
                    bDices[bIdx++] = i;
            }

            //  scoreCnt[i] : N / 2개의 주사위를 던져서 나오는 점수가 i인 경우의 수
            int[] aScoreCnt = new int[100 * N / 2 + 1];
            int[] bScoreCnt = new int[100 * N / 2 + 1];
            
            permutation(0, 0, aDices, aScoreCnt);
            permutation(0, 0, bDices, bScoreCnt);
            
            int aMinScore = Integer.MAX_VALUE;
            int aMaxScore = Integer.MIN_VALUE;
            int bMinScore = Integer.MAX_VALUE;
            int bMaxScore = Integer.MIN_VALUE;
            
            for(int i = 0; i < 100 * N / 2; i++) {
                if(aScoreCnt[i] > 0) {
                    aMinScore = Math.min(aMinScore, i);
                    aMaxScore = Math.max(aMaxScore, i);
                }
                if(bScoreCnt[i] > 0) {
                    bMinScore = Math.min(bMinScore, i);
                    bMaxScore = Math.max(bMaxScore, i);
                }
            }
           
            int win = 0;    //  A가 이기는 경우의 수
            
            for(int i = aMinScore; i <= aMaxScore; i++) {
                for(int j = bMinScore; j < i; j++)
                    win += aScoreCnt[i] * bScoreCnt[j];
            }
            
            if(win > maxWin) {
                maxWin = win;
                for(int i = 0; i < N / 2; i++)
                    answer[i] = aDices[i] + 1;
            }           
        
            return;
        }
        
        for(int i = start; i < N; i++) {
            selected[i] = true;
            combination(nth + 1, i + 1, selected);
            selected[i] = false;
        }
    }
    
    private void permutation(int nth, int sum, int[] dices, int[] scoreCnt) {
        if(nth == N / 2) {
            scoreCnt[sum]++;
            return;
        }
        
        int diceNum = dices[nth];   //  nth번째 주사위
        
        for(int i = 0; i < 6; i++)
            permutation(nth + 1, sum + DICE[diceNum][i], dices, scoreCnt);
    }
}
```