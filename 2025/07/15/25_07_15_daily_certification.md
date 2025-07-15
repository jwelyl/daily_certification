# 25_07_15_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 11037 중복 없는 수

[11037번: 중복 없는 수](http://boj.ma/11037/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
	private static final int INF = Integer.MAX_VALUE;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();

	private static int N;
	private static String nStr;
	private static int answer = INF;
	
	public static void main(String[] args) throws IOException {
		while(true) {
			String input = br.readLine();
			
			if(input == null) {
				System.out.print(sb);
				break;
			}
			
			N = Integer.parseInt(input);
			answer = INF;
			
			if(N >= 987_654_321) {
				sb.append("0\n");
				continue;
			}
			
			nStr = Integer.toString(N);
			
			//	nStr과 길이가 같으면서 N보다 큰 수 찾기
			backtracking(0, nStr.length(), new boolean[10], new char[nStr.length()], false);
			
			if(answer == INF) {	//	같은 길이 중에서는 큰 수가 없을 경우
				//	길이가 1 긴 수
				if(nStr.length() + 1 <= 9) {	//	1 ~ 9까지의 수를 한번씩 사용해야 하므로 길이는 9 이하여야 함
					answer = 0;
					
					for(int i = 1; i <= nStr.length() + 1; i++) {
						answer *= 10;
						answer += i;
					}
				}
			}
			
			sb.append(answer == INF ? 0 : answer).append("\n");
		}
	}	//	main-end
	
	private static void backtracking(int nth, int len, boolean[] used, char[] numArr, boolean ok) {
		if(nth == len) {
			if(ok) {
				int res = Integer.parseInt(new String(numArr));
				answer = Math.min(answer, res);
			}
			
			return;
		}
		
		if(!ok) {	//	아직 len - 1까지는 결론이 안났음
			int digit = nStr.charAt(nth) - '0';
			for(int i = Math.max(digit, 1); i <= 9; i++) {
				if(!used[i]) {
					if(i > digit) { 	//	len번째 수에서 결론이 날 경우
						used[i] = true;
						numArr[nth] = (char)(i + '0');
						backtracking(nth + 1, len, used, numArr, true);
						used[i] = false;
					}
					else {	//	len번째 수에서도 결론이 나지 않음
						used[i] = true;
						numArr[nth] = (char)(i + '0');
						backtracking(nth + 1, len, used, numArr, false);
						used[i] = false;
					}
				}
			}
		}
		else {	//	이미 len - 1까지에서 결론이 났음
			for(int i = 1; i <= 9; i++) {
				if(!used[i]) {	//	아직 사용하지 않은 가장 작은 숫자 찾기
					used[i] = true;
					numArr[nth] = (char)(i + '0');
					backtracking(nth + 1, len, used, numArr, true);
					used[i] = false;
					break;
				}
			}
		}
	}
}	//	Main-class-end
```