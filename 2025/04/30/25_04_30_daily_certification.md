# 25_04_30_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 17619 개구리 점프

[17619번: 개구리 점프](http://boj.ma/17619/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Collections;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int N; // 통나무 개수
	private static int Q; // 질의 개수

	private static final List<Log> logs = new ArrayList<>(); // 통나무 리스트

	private static int[] parents; // 분리집합 자료구조

	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		Q = Integer.parseInt(st.nextToken());

		parents = new int[N + 1];
		for (int num = 1; num <= N; num++)
			parents[num] = num;

		for (int num = 1; num <= N; num++) {
			st = new StringTokenizer(br.readLine());
			int x1 = Integer.parseInt(st.nextToken());
			int x2 = Integer.parseInt(st.nextToken());

			logs.add(new Log(num, x1, x2));
		}

		Collections.sort(logs);

		for (int i = 0; i < N; i++) {
			Log cur = logs.get(i);

			for (int j = i + 1; j < N; j++) {
				Log next = logs.get(j);

				if (cur.x2 >= next.x1) {
					union(cur.num, next.num);
				} else
					break;
			}
		}

		for (int q = 0; q < Q; q++) {
			st = new StringTokenizer(br.readLine());
			int num1 = Integer.parseInt(st.nextToken());
			int num2 = Integer.parseInt(st.nextToken());

			sb.append(find(num1) == find(num2) ? 1 : 0).append("\n");
		}

		System.out.print(sb);
	} // main-end

	private static int find(int num) {
		if (num == parents[num])
			return num;

		return parents[num] = find(parents[num]);
	}

	private static void union(int num1, int num2) {
		num1 = find(num1);
		num2 = find(num2);

		if (num1 != num2)
			parents[num1] = num2;
	}

	private static class Log implements Comparable<Log> {
		public final int num; // 통나무 번호
		public final int x1;
		public final int x2;

		public Log(int num, int x1, int x2) {
			this.num = num;
			this.x1 = x1;
			this.x2 = x2;
		}

		@Override
		public int compareTo(Log other) {
			return Integer.compare(this.x1, other.x1); // x1이 작은 순서로 정렬
		}

		@Override
		public String toString() {
			return "Log " + this.num;
		}
	}
} // Main-class-end
```