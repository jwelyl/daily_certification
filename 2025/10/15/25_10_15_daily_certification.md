# 25_10_15_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1888 곰팡이

[1888번: 곰팡이](http://boj.ma/1888/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.ArrayDeque;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;
	private static int M;

	private static char[][] map;
	private static int[] parents;
	private static int dsCnt = 0;

	private static final Queue<int[]> queue = new ArrayDeque<>();

	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());

		parents = new int[N * M];
		for (int v = 0; v < N * M; v++)
			parents[v] = v;

		map = new char[N][M];
		for (int y = 0; y < N; y++) {
			map[y] = br.readLine().toCharArray();
			for (int x = 0; x < M; x++) {
				if (map[y][x] != '0') {
					dsCnt++;

					int num = y * M + x;
					for (int d = 2; d < 4; d++) {
						int ny = y + dy[d];
						int nx = x + dx[d];

						if (isIn(ny, nx) && map[ny][nx] != '0') {
							int nnum = ny * M + nx;
							union(num, nnum);
						}
					}

					queue.offer(new int[] { y, x, map[y][x] - '0' });
				}
			}
		}

		System.out.println(bfs());
	} // main-end

	private static int bfs() {
		if (dsCnt == 1) // 처음부터 곰팡이 한 덩어리일 경우
			return 0;

		int time = 1;

		while (!queue.isEmpty()) {
			int size = queue.size();

			for (int s = 0; s < size; s++) {
				int[] cur = queue.poll();
				int cy = cur[0];
				int cx = cur[1];
				int ck = cur[2];
				int cnum = cy * M + cx;

				for (int ny = cy - ck; ny <= cy + ck; ny++) {
					for (int nx = cx - ck; nx <= cx + ck; nx++) {
						if (!isIn(ny, nx))
							continue;

						int nnum = ny * M + nx;

						if (map[ny][nx] == '0') {
							dsCnt++;
							map[ny][nx] = (char) ('0' + ck);
							
                            if(union(cnum, nnum))
                                return time;

							for (int d = 0; d < 4; d++) {
								int nny = ny + dy[d];
								int nnx = nx + dx[d];
								int nnnum = nny * M + nnx;

								if (isIn(nny, nnx) && map[nny][nnx] != '0') {
									if(union(nnum, nnnum))
                                       return time;
                                }
							}

							queue.offer(new int[] { ny, nx, ck });
						} else {
							if(union(cnum, nnum))
                                return time;

							if (map[ny][nx] < (char) ('0' + ck)) {
								map[ny][nx] = (char) ('0' + ck);
								queue.offer(new int[] { ny, nx, ck });
							}
						}
					}
				}
			}
            
            time++;
		}

		return -1;
	}

	private static int find(int v) {
		return v == parents[v] ? v : (parents[v] = find(parents[v]));
	}

	private static boolean union(int v1, int v2) {
		v1 = find(v1);
		v2 = find(v2);

		if (v1 != v2) {
			parents[v2] = v1;
			dsCnt--;
		}
        
        return dsCnt == 1;
	}

	private static final int[] dy = { 0, 1, 0, -1 };
	private static final int[] dx = { 1, 0, -1, 0 };

	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}
} // Main-class-end
```

### BOJ 10800 컬러볼

[10800번: 컬러볼](http://boj.ma/10800/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
    private static final int MAX = 200_001;    //    볼 색 범위 (1 ~ 200,000)
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;    //    볼 개수
    
    private static final List<Ball> balls = new ArrayList<>();
    
    private static int ballIdx = 0;
    private static int sum = 0;
    private static final int[] colors = new int[MAX];    //    colors[i] : [0, ballIdx]까지 볼들 중에서 색이 i인 볼들의 크기 합
    
    private static int[] answers;    //    answers[i] : i번째 볼이 잡은 볼 크기 합
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        answers = new int[N];
        
        for(int b = 0; b < N; b++) {
            st = new StringTokenizer(br.readLine());
            int color = Integer.parseInt(st.nextToken());
            int size = Integer.parseInt(st.nextToken());
            
            balls.add(new Ball(b, color, size));
        }
        
        Collections.sort(balls);    //    크기순 정렬
        
        for(int i = 0; i < N; i++) {
            Ball cBall = balls.get(i);    //    현재 볼
            int cnum = cBall.num;
            int ccolor = cBall.color;
            int csize = cBall.size;
            
            while(balls.get(ballIdx).size < csize) {    //    현재 볼이 잡을 가능성이 있는 볼들에 대해
                sum += balls.get(ballIdx).size;    //    현재 볼들보다 크기 작은 볼 크기 추가
                colors[balls.get(ballIdx).color] += balls.get(ballIdx).size;    //    색깔에 따른 크기 추가
                ballIdx++;
            }
            
            //    현재 볼 색깔과 같은 볼들의 크기 합 배제
            answers[cnum] = sum - colors[ccolor];
        }
        
        for(int i = 0; i < N; i++)
            sb.append(answers[i]).append("\n");
        
        System.out.print(sb);
    }    //    main-end
    
    private static class Ball implements Comparable<Ball> {
        public int num;        //    볼 번호
        public int color;      //    볼 색깔
        public int size;       //    볼 크기
     
        public Ball(int num, int color, int size) {
            this.num = num;
            this.color = color;
            this.size = size;
        }
        
        @Override
        public int compareTo(Ball other) {
            return Integer.compare(this.size, other.size);    //    볼 크기 순으로 오름차순 정렬
        }
    }
}    //    Main-class-end
```