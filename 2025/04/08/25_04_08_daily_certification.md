# 25_04_08_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1099 알 수 없는 문장

[1099번: 알 수 없는 문장](http://boj.ma/1099/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;

public class Main {
	private static final int NONE = 1_000_000_000;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

	private static String input;
	private static int N;
	private static String[] words;
	
	private static int[] dp;
	
	private static int answer = NONE;
	
	public static void main(String[] args) throws IOException {
		input = br.readLine();
		
		N = Integer.parseInt(br.readLine());
		
		words = new String[N];
		for(int i = 0; i < N; i++)
			words[i] = br.readLine();
		
		dp = new int[input.length() + 1];
		Arrays.fill(dp, NONE);
		dp[0] = 0;
		
		for(int i = 0; i < input.length(); i++) {
			for(int start = 0; start <= i; start++) {
				String target = input.substring(start, i + 1);
				
				for(int j = 0; j < N; j++) {
					String word = words[j];
					
					if(canMatch(target, word))
						dp[i + 1] = Math.min(dp[i + 1], dp[start] + score(word, target)); 
				}
			}
		}
		
		answer = dp[input.length()];
		System.out.println(answer == NONE ? -1 : answer);
	} //	main-end
	
	private static boolean canMatch(String str1, String str2) {
		if(str1.length() != str2.length())
			return false;
		
		int[] cnts = new int[26];
		
		for(int i = 0; i < str1.length(); i++) {
			char ch = str1.charAt(i);
			
			int cnt = cnts[ch - 'a'];
			cnts[ch - 'a'] = cnt + 1;
		}
		
		for(int i = 0; i < str2.length(); i++) {
			char ch = str2.charAt(i);
			
			int cnt = cnts[ch - 'a'];
			
			if(cnt == 0)
				return false;
			
			cnts[ch - 'a'] = cnt - 1;
		}
		
		return true;
	}
	
	private static int score(String std, String str) {
		int res = 0;
		
		for(int i = 0; i < std.length(); i++) {
			if(std.charAt(i) != str.charAt(i))
				res++;
		}
		
		return res;
	}
} //	Main-class-end
```