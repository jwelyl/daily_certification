# 24_09_15_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 14868 문명

[](https://www.acmicpc.net/problem/14868)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int SAVAGE = 0; 
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;	//	세계 크기 N * N
	private static int K;	//	초기 문명 개수 K
	
	private static int[][] worlds;	//	세계
	
	private static final Queue<int[]> queue = new LinkedList<>();
	
	private static int remains = 0;	//	남은 문명 개수
	private static int years = 0;	//	남은 문명 개수가 1개가 될 때 까지 걸리는 햇수
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		worlds = new int[N][N];
		parents = new int[K + 1];
		for(int k = 1; k <= K; k++)
			parents[k] = k;
		
		remains = K;	//	초기 문명 개수는 K개
		
		//	초기 문명 입력 [y 좌표, x 좌표, 문명 번호]
		for(int k = 1; k <= K; k++) {
			st = new StringTokenizer(br.readLine());
			int y = Integer.parseInt(st.nextToken()) - 1;
			int x = Integer.parseInt(st.nextToken()) - 1;
			int num = k;
			
			worlds[y][x] = num;
			
			queue.offer(new int[] {y, x, num});
			
			for(int d = 0; d < 4; d++) {	//	현재 입력받은 초기 문명의 주변 문명 확인
				int ny = y + dy[d];
				int nx = x + dx[d];	//	주변 칸
				
				if(isIn(ny, nx)) {	//	주변 칸이 세계를 벗어나지 않는 경우
					int nnum = worlds[ny][nx];	//	주변 칸 번호
					
					if(nnum != SAVAGE && num != nnum)	//	주변 칸이 현재 입력받은 문명과 다른 문명이 존재할 경우
						union(num, nnum);	//	num 문명과 cnum 문명을 합치기	
				}
			}
		}
		
		while(remains > 1) {	//	문명 개수가 1개가 될 때까지
			years++;
			simulation();
		}
		
		System.out.println(years);
	}	//	main-end
	
	private static void simulation() {
		int size = queue.size();	//	현재 세계 상에 존재하는 문명의 위치 개수 (이미 큐에 들어갔다가 빠진 것들은 제외)
		
		for(int s = 0; s < size; s++) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];		//	문명 위치의 좌표
			int cnum = cur[2];		//	해당 문명의 번호
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];	//	인접 칸 좌표
				
				if(isIn(ny, nx)) {	//	인접 칸이 세계를 벗어나지 않을 경우
					int nnum = worlds[ny][nx];	//	인접 문명의 번호
					
					if(nnum == SAVAGE) {	//	인접 칸이 미개할 경우
						worlds[ny][nx] = cnum;	//	해당 칸에 cnum 문명이 진출하기
						queue.offer(new int[] {ny, nx, cnum});
					}
					
					for(int dd = 0; dd < 4; dd++) {	//	현재 입력받은 초기 문명의 주변 문명 확인
						int nny = ny + dy[dd];
						int nnx = nx + dx[dd];	//	진출한 칸의 주변 칸
						
						if(isIn(nny, nnx)) {	//	진출한 칸의 주변 칸이 세계를 벗어나지 않는 경우
							int nnnum = worlds[nny][nnx];	//	진출한 칸의 주변 칸 번호
							
							if(nnnum != SAVAGE && nnnum != cnum)	//	진출한 칸의 주변 칸에 문명이 존재하고 현재 문명 번호와 다를 경우
								union(cnum, nnnum);	//	합쳐보기
						}
					}
				}
			}
		}
	}
	
	private static int[] parents;	//	parents[i] : i가 속한 Disjoint Set에서 i의 부모, Disjoint Set에서 우두머리일 경우 자기 자신이 부모임
	
	//	v가 속한 Disjoint Set의 우두머리를 찾아서 반환
	private static int findDS(int v) {
		if(v == parents[v])	//	v 자신이 우두머리일 경우
			return v;
		
		return parents[v] = findDS(parents[v]);	//	v의 부모의 우두머리를 찾아서, v의 부모로 삼아서 반환
	}
	
	//	v1, v2가 속한 Disjoint Set이 서로 다를 경우, 하나로 합치고 남은 문명 개수 1 감소
	private static void union(int v1, int v2) {
		int v1DS = findDS(v1);
		int v2DS = findDS(v2);	//	v1, v2가 속한 Disjoint Set의 우두머리
		
		if(v1DS != v2DS) {	//	둘이 같은 Disjoin Set이 아닐 경우
			parents[v1DS] = v2DS;	//	v2DS의 부모를 v1DS로 하여, 둘을 합침
			remains--;	//	남은 문명 개수 1 감소
		}
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
}	//	Main-class-end
```