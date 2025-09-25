# 25_09_25_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 27591 Three Dice

[27591번: Three Dice](http://boj.ma/27591/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.HashSet;
import java.util.List;
import java.util.Map;
import java.util.Set;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	
	private static int N;
	
	private static char[][] words;
	
	private static final Set<Character> uniqueChars = new HashSet<>();
	private static final List<Character> charList = new ArrayList<>();
	private static final Map<Character, Integer> diceMap = new HashMap<>();
	
	private static boolean find = false;

    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        words = new char[N][3];

        for (int i = 0; i < N; i++) {
            words[i] = br.readLine().toCharArray();
            for (char c : words[i]) 
            	uniqueChars.add(c);
        }

        if (uniqueChars.size() > 6 * 3) {
            System.out.println(0);
            return;
        }

        charList.addAll(uniqueChars);
        dfs(0);

        if (!find) 
        	System.out.println(0);
    }	//	main-end

    private static void dfs(int nth) {
        if (find) 
        	return;
        
        if (nth == charList.size()) {
            if (checkAllWords()) {
                StringBuilder[] dice = {new StringBuilder(), new StringBuilder(), new StringBuilder()};
                for (char c : charList)
                    dice[diceMap.get(c)].append(c);

                // 각 주사위 6칸 넘으면 안됨
                if (dice[0].length() > 6 || dice[1].length() > 6 || dice[2].length() > 6) 
                	return;

                // 남은 알파벳으로 채우기
                fillRemaining(dice);

                // 최종 출력
                System.out.println(dice[0] + " " + dice[1] + " " + dice[2]);
                find = true;
            }
            
            return;
        }

        char ch = charList.get(nth);	//	nth번째 문자
        for (int idx = 0; idx < 3; idx++) {
            diceMap.put(ch, idx);	//	nth번째 문자를 idx 주사위에 배치
            
            if (partialCheck())	//	유효할 경우
                dfs(nth + 1);
            
            diceMap.remove(ch);
        }
    }

    private static boolean checkAllWords() {
        for (char[] word : words) {
        	//	단어의 어떤 문자가 주사위에 할당되지 않은 경우
            if (!(diceMap.containsKey(word[0]) && diceMap.containsKey(word[1]) && diceMap.containsKey(word[2])))
                return false;
            
            int d0 = diceMap.get(word[0]);
            int d1 = diceMap.get(word[1]);
            int d2 = diceMap.get(word[2]);
            
            if (d0 == d1 || d1 == d2 || d2 == d0) //	서로 다른 문자가 같은 주사위에 할당된 경우
            	return false;
        }
        
        return true;
    }

    private static boolean partialCheck() {
        for (char[] word : words) {
            Set<Integer> used = new HashSet<>();
            for (char ch : word) {	//	단어에 쓰인 문자
                if (diceMap.containsKey(ch)) {	//	문자가 주사위에 할당되었을 경우
                    if (!used.add(diceMap.get(ch))) 	//	해당 단어의 다른 문자가 해당 주사위를 쓰고 있을 경우
                    	return false;
                }
            }
        }
        
        return true;
    }

    private static void fillRemaining(StringBuilder[] dice) {
        Set<Character> used = new HashSet<>();
        for (StringBuilder sb : dice) {
            for (int i = 0; i < sb.length(); i++) {
                used.add(sb.charAt(i));
            }
        }

        for (char c = 'a'; c <= 'z'; c++) {
            if (used.size() == 18) break;
            if (!used.contains(c)) {
                int idx = minIndex(dice);
                if (dice[idx].length() < 6) {
                    dice[idx].append(c);
                    used.add(c);
                }
            }
        }
    }

    private static int minIndex(StringBuilder[] dice) {
        int idx = 0;
        
        for (int i = 1; i < 3; i++)
            if (dice[i].length() < dice[idx].length()) 
            	idx = i;

        return idx;
    }
}	//	Main-class-ne
```