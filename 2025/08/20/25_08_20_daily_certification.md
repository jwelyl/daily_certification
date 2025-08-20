# 25_08_20_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 26302 Japanski Junak

[26302번: Japanski Junak](http://boj.ma/26302/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
	private static final char EMPTY = '.';

	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();

	private static final char[][] sudoku = new char[9][9];

	private static int emptyCnt = 0;

	public static void main(String[] args) throws IOException {
		for (int y = 0; y < 9; y++) {
			sudoku[y] = br.readLine().toCharArray();
			for (int x = 0; x < 9; x++) {
				if (sudoku[y][x] == EMPTY)
					emptyCnt++;
			}
		}

		if (invalid()) // 주어진 스도쿠에 오류가 있을 경우
			error();

		if (emptyCnt != 0) // 에러는 없지만 아직 완성이 안되었을 경우
			crossHatching();

		// 에러가 없을 경우
		for (int y = 0; y < 9; y++) {
			for (int x = 0; x < 9; x++)
				sb.append(sudoku[y][x]);
			sb.append("\n");
		}

		System.out.print(sb);
	} // main-end

	private static void crossHatching() {
		boolean changed;
		do {
			changed = false;
			for (char digit = '1'; digit <= '9'; digit++) {
				boolean[][] used = new boolean[9][9];

				for (int y = 0; y < 9; y++) {
					for (int x = 0; x < 9; x++)
						if (sudoku[y][x] == digit)
							markConstraints(y, x, used);
				}

				for (int by = 0; by < 9; by += 3) {
					for (int bx = 0; bx < 9; bx += 3) {
						if (boxHasDigit(digit, by, bx))
							continue;

						int cnt = 0;
						int cy = -1;
						int cx = -1;

						for (int y = by; y < by + 3; y++) {
							for (int x = bx; x < bx + 3; x++) {
								if (sudoku[y][x] == EMPTY && !used[y][x]) {
									cnt++;
									cy = y;
									cx = x;
								}
							}
						}

						if (cnt == 0)
							error();
						if (cnt == 1) {
							sudoku[cy][cx] = digit;
							emptyCnt--;
							changed = true;

							markConstraints(cy, cx, used);
						}
					}
				}
			}
		} while (changed);
	}

	private static void markConstraints(int y, int x, boolean[][] used) {
		for (int i = 0; i < 9; i++) {
			used[y][i] = true;
			used[i][x] = true;
		}

		int sy = (y / 3) * 3;
		int sx = (x / 3) * 3;

		for (int ny = sy; ny < sy + 3; ny++)
			for (int nx = sx; nx < sx + 3; nx++)
				used[ny][nx] = true;

		used[y][x] = true;
	}

	private static boolean boxHasDigit(char digit, int by, int bx) {
		for (int y = by; y < by + 3; y++)
			for (int x = bx; x < bx + 3; x++)
				if (sudoku[y][x] == digit)
					return true;
		return false;
	}

	private static boolean invalid() {
		for (int y = 0; y < 9; y++) {
			for (int x = 0; x < 9; x++) {
				if (sudoku[y][x] != EMPTY) {
					for (int d = 0; d < 4; d++) {
						for (int k = 1;; k++) {
							int ny = y + dy[d] * k;
							int nx = x + dx[d] * k;

							if (!isIn(ny, nx))
								break;

							if (sudoku[ny][nx] == sudoku[y][x])
								return true;
						}
					}
				}
			}
		}

		for (int y = 0; y < 9; y += 3) {
			for (int x = 0; x < 9; x += 3) {
				boolean[] digits = new boolean[10];

				for (int ny = y; ny < y + 3; ny++) {
					for (int nx = x; nx < x + 3; nx++) {
						if (sudoku[ny][nx] == EMPTY)
							continue;

						if (digits[sudoku[ny][nx] - '0'])
							return true;

						digits[sudoku[ny][nx] - '0'] = true;
					}
				}
			}
		}

		return false;
	}

	private static void error() {
		System.out.println("GRESKA");
		System.exit(0);
	}

	private static final int[] dy = { 0, 1, 0, -1 };
	private static final int[] dx = { 1, 0, -1, 0 };

	private static boolean isIn(int y, int x) {
		return (0 <= y && y < 9) && (0 <= x && x < 9);
	}
} // Main-class-end
```