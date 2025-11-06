# 25_11_06_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2879 코딩은 예쁘게

[2879번: 코딩은 예쁘게](http://boj.ma/2879/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N; 		// 코드 줄의 수
	private static int[] diff;  // 각 줄별로 필요한 변화량

	private static int cnt0 = 0;
	private static int answer = 0;

	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());

		diff = new int[N];
		st = new StringTokenizer(br.readLine());
		for (int i = 0; i < N; i++)
			diff[i] = Integer.parseInt(st.nextToken());
		st = new StringTokenizer(br.readLine());
		for (int i = 0; i < N; i++) {
			diff[i] = Integer.parseInt(st.nextToken()) - diff[i];
			if (diff[i] == 0)
				cnt0++;
		}

		while (cnt0 < N) {
			List<int[]> list = new ArrayList<>();

			int i = 0;
			while (i < N) {
				if (diff[i] == 0) {
					i++;
					continue;
				}

				int sign = diff[i] > 0 ? 1 : -1;
				int start = i;
				int value = diff[i];

				while (i + 1 < N && diff[i + 1] * sign > 0) {
					i++;
					value = (sign > 0) ? Math.min(value, diff[i]) : Math.max(value, diff[i]);
				}

				int end = i;
				list.add(new int[] { start, end, value });
				i++;
			}

			for (int[] segment : list) {
				int from = segment[0];
				int to = segment[1];
				int value = segment[2];

				answer += Math.abs(value);

				for (int j = from; j <= to; j++) {
					diff[j] -= value;
					if (diff[j] == 0)
						cnt0++;
				}
			}
		}

		System.out.println(answer);
	} // main-end
} // Main-class-end
```