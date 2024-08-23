# 24_08_24_daily_certification

```java
import java.util.*;
import java.io.*;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int R;
	private static int C;

	private static char[][] ruins;
	private static boolean[][] used;
	private static int[][] prefixSum;

	private static int cnt = 0; // x 칸 개수

	private static int luy1 = -1;
	private static int lux1 = -1;	//	첫 번째 정사각형 좌상단 좌표
	private static int len1;
	private static int luy2 = -1;
	private static int lux2 = -1;	//	두 번째 정사각형 좌상단 좌표
	private static int len2;

	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		R = Integer.parseInt(st.nextToken());
		C = Integer.parseInt(st.nextToken());

		ruins = new char[R][C];
		for (int r = 0; r < R; r++)
			ruins[r] = br.readLine().toCharArray();

		prefixSum = new int[R + 1][C + 1];
		used = new boolean[R + 1][C + 1];

		for (int r = 1; r <= R; r++) {
			for (int c = 1; c <= C; c++) {
				int add = ruins[r - 1][c - 1] == 'x' ? 1 : 0;

				prefixSum[r][c] = prefixSum[r - 1][c] + prefixSum[r][c - 1] - prefixSum[r - 1][c - 1] + add;
				cnt += add;
			}
		}

		// 첫 번째 건축물 구하기
		FIRST: for (int lux = 1; lux <= C; lux++) {
			for (int luy = 1; luy <= R; luy++) {
				if (ruins[luy - 1][lux - 1] != 'x')
					continue;

				int len = Math.min(R, C);
				while (len >= 1) {
					int rdy = luy + len - 1;
					int rdx = lux + len - 1;	//	우하단 좌표

					if (!isIn(rdy, rdx)) {
						len--;
						continue;
					}

					if (partialSum(luy, lux, rdy, rdx) == len * len) {
						luy1 = luy;
						lux1 = lux;
						len1 = len;

						for (int i = luy; i <= rdy; i++) {
							for (int j = lux; j <= rdx; j++)
								used[i][j] = true;
						}

						break FIRST;
					}

					len--;
				}
			}
		}

		SECOND1: // 두 번째 정사각형의 좌상단 찾기
		for (int lux = 1; lux <= C; lux++) {
			for (int luy = 1; luy <= R; luy++) {
				if (ruins[luy - 1][lux - 1] != 'x' || used[luy][lux])
					continue;

				int len = Math.min(R, C);
				while (len >= 1) {
					int rdy = luy + len - 1;
					int rdx = lux + len - 1;	//	우하단 좌표

					if (!isIn(rdy, rdx)) {
						len--;
						continue;
					}

					if (partialSum(luy, lux, rdy, rdx) == len * len) {
						luy2 = luy;
						lux2 = lux;
						len2 = len;
						break SECOND1;
					}

					len--;
				}
			}
		}
		
		if(len1 * len1 + len2 * len2 - overlappedCnt(luy1, lux1, len1, luy2, lux2, len2) == cnt) {
			if(luy2 == -1 && lux2 == -1) {	//	두 정사각형이 완전히 일치할 경우
				luy2 = luy1;
				lux2 = lux1;
				len2 = len1;
			}
			
			System.out.println(luy1 + " " + lux1 + " " + len1);
			System.out.println(luy2 + " " + lux2 + " " + len2);
			return;
		}
		
		SECOND2: // 두 번째 정사각형의 좌하단 찾기
			for (int ldx = 1; ldx <= C; ldx++) {
				for (int ldy = R; ldy >= 1; ldy--) {
					if (ruins[ldy - 1][ldx - 1] != 'x' || used[ldy][ldx])
						continue;

					int len = Math.min(R, C);
					
					while (len >= 1) {
						int luy = ldy - len + 1;
						int lux = ldx;					//	좌상단
						int rdy = luy + len - 1;
						int rdx = lux + len - 1;		//	우하단
						
						if(!isIn(luy, lux) || !isIn(rdy, rdx)) {
							len--;
							continue;
						}

						if (partialSum(luy, lux, rdy, rdx) == len * len) {
							luy2 = luy;
							lux2 = lux;
							len2 = len;
							break SECOND2;
						}

						len--;
					}
				}
			}
		
		if(len1 * len1 + len2 * len2 - overlappedCnt(luy1, lux1, len1, luy2, lux2, len2) == cnt) {
			System.out.println(luy1 + " " + lux1 + " " + len1);
			System.out.println(luy2 + " " + lux2 + " " + len2);
			return;
		}
		
		SECOND3: // 두 번째 정사각형의 우상단 찾기
			for (int rux = C; rux >= 1; rux--) {
				for (int ruy = 1; ruy <= R; ruy++) {
					if (ruins[ruy - 1][rux - 1] != 'x' || used[ruy][rux])
						continue;

					int len = Math.min(R, C);
					
					while (len >= 1) {
						int luy = ruy;
						int lux = rux - len + 1;		//	좌상단
						int rdy = luy + len - 1;
						int rdx = lux + len - 1;		//	우하단
						
						if(!isIn(luy, lux) || !isIn(rdy, rdx)) {
							len--;
							continue;
						}

						if (partialSum(luy, lux, rdy, rdx) == len * len) {
							luy2 = luy;
							lux2 = lux;
							len2 = len;
							break SECOND3;
						}

						len--;
					}
				}
			}
		
		if(len1 * len1 + len2 * len2 - overlappedCnt(luy1, lux1, len1, luy2, lux2, len2) == cnt) {
			System.out.println(luy1 + " " + lux1 + " " + len1);
			System.out.println(luy2 + " " + lux2 + " " + len2);
			return;
		}
		
		SECOND4: // 두 번째 정사각형의 우하단 찾기
			for (int rdx = C; rdx >= 1; rdx--) {
				for (int rdy = R; rdy >= R; rdy--) {
					if (ruins[rdy - 1][rdx - 1] != 'x' || used[rdy][rdx])
						continue;

					int len = Math.min(R, C);
					
					while (len >= 1) {
						int luy = rdy - len + 1;
						int lux = rdx - len + 1;		//	좌상단
						
						if(!isIn(luy, lux)) {
							len--;
							continue;
						}

						if (partialSum(luy, lux, rdy, rdx) == len * len) {
							luy2 = luy;
							lux2 = lux;
							len2 = len;
							break SECOND4;
						}

						len--;
					}
				}
			}
		
		if(len1 * len1 + len2 * len2 - overlappedCnt(luy1, lux1, len1, luy2, lux2, len2) == cnt) {
			System.out.println(luy1 + " " + lux1 + " " + len1);
			System.out.println(luy2 + " " + lux2 + " " + len2);
			return;
		}
	} // main-end
	
	private static boolean isIn(int y, int x) {
		return (1 <= y && y <= R) && (1 <= x && x <= C);
	}

	private static int partialSum(int luy, int lux, int rdy, int rdx) {
		return prefixSum[rdy][rdx] - prefixSum[rdy][lux - 1] - prefixSum[luy - 1][rdx] + prefixSum[luy - 1][lux - 1];
	}

	// 좌상단이 (luy1, lux1)이고 한 변 길이가 len1인 정사각형과 좌상단이 (luy2, lux2)이고 한 변 길이가 len2인
	// 정사각형의 중첩된 칸 수
	private static int overlappedCnt(int luy1, int lux1, int len1, int luy2, int lux2, int len2) {
		int rdy1 = luy1 + len1 - 1;
		int rdx1 = lux1 + len1 - 1; // 첫 번째 정사각형 우하단 좌표
		int rdy2 = luy2 + len2 - 1;
		int rdx2 = lux2 + len2 - 1; // 두 번째 정사각형 우하단 좌표

		int oluy = Math.max(luy1, luy2);
		int olux = Math.max(lux1, lux2); // 겹치는 부분 직사각형의 좌상단 좌표
		int ordy = Math.min(rdy1, rdy2);
		int ordx = Math.min(rdx1, rdx2); // 겹치는 부분 직사각형의 우하단 좌표

		if (ordx - olux + 1 > 0 && ordy - oluy + 1 > 0) // 겹치는 부분 존재할 경우
			return (ordx - olux + 1) * (ordy - oluy + 1);

		return 0;
	}
} // Main-class-end
```