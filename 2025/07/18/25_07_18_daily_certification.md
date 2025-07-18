# 25_07_18_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 15811 복면산?!

[15811번: 복면산?!](http://boj.ma/15811/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final long[] pows = new long[18];
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static final List<Integer>[] map = new ArrayList[26];
	private static final List<Character> chList = new ArrayList<>();
	
	private static final char[][] words = new char[3][];
	private static final boolean[] used = new boolean[10];
	private static final int[] selected = new int[26];
	
	public static void main(String[] args) throws IOException {
		pows[0] = 1;
		for(int i = 1; i < 18; i++)
			pows[i] = pows[i - 1] * 10;
		
		st = new StringTokenizer(br.readLine());
		for(int i = 0; i < 3; i++)
			words[i] = st.nextToken().toCharArray();
		
		for(int i = 0; i < 3; i++) {
			char[] word = words[i];
			int len = word.length;
			
			for(int j = len - 1; j >= 0; j--) {
				char ch = word[j];
				
				if(map[ch - 'A'] == null) {
					map[ch - 'A'] = new ArrayList<>();
					chList.add(ch);
				}
				
				map[ch - 'A'].add(i * 100 + (len - 1 - j));
			}
		}
	
		if(chList.size() > 10) {	//	사용된 대문자가 10개를 넘을 경우 0 ~ 9만으로 모든 문자를 다르게 대응시킬 수 없음
			System.out.println("NO");
			return;
		}
		
		backtracking(0, chList.size(), used, selected);
		
		System.out.println("NO");
	} //	main-end
	
	private static void backtracking(int nth, int size, boolean[] used, int[] selected) {
		if(nth == size) {	//	모든 대문자에 숫자를 대응시켰을 경우
			long[] nums = new long[3];
			
			for(char ch : chList) {
				List<Integer> posList = map[ch - 'A'];	//	ch가 사용된 위치 리스트
				int digit = selected[ch - 'A'];			//	ch가 배정된 숫자
				
				for(int pos : posList) {
					int numNth = pos / 100;
					int idx = pos % 100;
					
					nums[numNth] += pows[idx] * digit; 
				}
			}
			
			if(nums[0] + nums[1] == nums[2]) {
				System.out.println("YES");
				System.exit(0);
			}
			
			return;
		}
		
		char ch = chList.get(nth);	//	숫자를 대응시킬 문자
		for(int digit = 0; digit < 10; digit++) {
			if(!used[digit]) {	//	digit이 사용되지 않았을 경우
				used[digit] = true;
				selected[ch - 'A'] = digit;
				backtracking(nth + 1, size, used, selected);
				used[digit] = false;
			}
		}
	}
} //	Main-class-end
```