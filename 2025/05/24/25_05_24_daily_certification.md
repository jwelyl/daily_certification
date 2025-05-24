# 25_05_24_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 3108 로고

[3108번: 로고](http://boj.ma/3108/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;
	private static Rectangle[] rectangles;

	private static int[] parents;
	private static int rCnt;

	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());

		rectangles = new Rectangle[N + 1];
		rectangles[0] = new Rectangle(0, 0, 0, 0);
		parents = new int[N + 1];
		rCnt = N + 1;

		for (int r = 1; r <= N; r++) {
			st = new StringTokenizer(br.readLine());
			rectangles[r] = new Rectangle(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken()),
					Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken()));
			parents[r] = r;
		}

		for (int r1 = 0; r1 < N; r1++) {
			for (int r2 = r1 + 1; r2 <= N; r2++) {
				if (Rectangle.intersect(rectangles[r1], rectangles[r2]))
					union(r1, r2);
			}
		}

		System.out.println(rCnt - 1);
	} // main-end

	private static int find(int r) {
		return r == parents[r] ? r : (parents[r] = find(parents[r]));
	}

	private static void union(int r1, int r2) {
		r1 = find(r1);
		r2 = find(r2);

		if (r1 != r2) {
			parents[r2] = r1;
			rCnt--;
		}
	}

	private static class Rectangle {
		public int lx;
		public int ly;
		public int rx;
		public int ry;

		public Rectangle(int lx, int ly, int rx, int ry) {
			this.lx = lx;
			this.ly = ly;
			this.rx = rx;
			this.ry = ry;
		}

		// 두 직사각형이 겹치는지 판단
		public static boolean intersect(Rectangle r1, Rectangle r2) {
			if ((r1.lx < r2.lx && r2.rx < r1.rx && r1.ly < r2.ly && r2.ry < r1.ry)
					|| (r1.lx > r2.lx && r2.rx > r1.rx && r1.ly > r2.ly && r2.ry > r1.ry) || r1.rx < r2.lx
					|| r2.rx < r1.lx || r1.ry < r2.ly || r2.ry < r1.ly) {
				return false;
			}
			return true;
		}
	}
} // Main-class-end
```