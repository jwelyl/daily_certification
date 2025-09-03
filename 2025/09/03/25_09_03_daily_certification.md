# 25_09_03_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 14956 philosopher's Walk

[14956번: Philosopher’s Walk](http://boj.ma/14956/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;
	private static int M;

	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());

		System.out.println(recursion(N, 0, M, 1, 1));
	} // main-end

	private static Pos recursion(int n, int rot, int nth, int y, int x) {
		if (n == 1)
			return new Pos(y, x);

		int size = n / 2;
		int area = size * size;

		if (rot == 0) {
			if (1 <= nth && nth <= area)
				return recursion(size, 1, nth, y, x);
			else if (area < nth && nth <= 2 * area)
				return recursion(size, 0, nth - area, y + size, x);
			else if (2 * area < nth && nth <= 3 * area)
				return recursion(size, 0, nth - 2 * area, y + size, x + size);
			else
				return recursion(size, 3, nth - 3 * area, y + size - 1, x + 2 * size - 1);
		}
		else if (rot == 1) {
			if (1 <= nth && nth <= area)
				return recursion(size, 0, nth, y, x);
			else if (area < nth && nth <= 2 * area)
				return recursion(size, 1, nth - area, y, x + size);
			else if (2 * area < nth && nth <= 3 * area)
				return recursion(size, 1, nth - 2 * area, y + size, x + size);
			else 
				return recursion(size, 2, nth - 3 * area, y + 2 * size - 1, x + size - 1);
		}
		else if (rot == 2) {
			if (1 <= nth && nth <= area)
				return recursion(size, 3, nth, y, x);
			else if (area < nth && nth <= 2 * area)
				return recursion(size, 2, nth - area, y - size, x);
			else if (2 * area < nth && nth <= 3 * area)
				return recursion(size, 2, nth - 2 * area, y - size, x - size);
			else
				return recursion(size, 1, nth - 3 * area, y - size + 1, x - 2 * size + 1);
		}
		else {
			if (1 <= nth && nth <= area)
				return recursion(size, 2, nth, y, x);
			else if (area < nth && nth <= 2 * area)
				return recursion(size, 3, nth - area, y, x - size);
			else if (2 * area < nth && nth <= 3 * area)
				return recursion(size, 3, nth - 2 * area, y - size, x - size);
			else
				return recursion(size, 0, nth - 3 * area, y - 2 * size + 1, x - size + 1);
		}
	}

	private static class Pos {
		public int y;
		public int x;

		public Pos(int y, int x) {
			this.y = y;
			this.x = x;
		}

		@Override
		public String toString() {
			return this.x + " " + this.y;
		}
	}
} // Main-class-end
```