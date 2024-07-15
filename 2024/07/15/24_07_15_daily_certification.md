# 24_07_15_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2243 사탕 상자

[](https://www.acmicpc.net/problem/2243)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX = 1_000_000 - 1;	//	사탕 맛 최대
	private static final int HEIGHT = (int)Math.ceil(Math.log(MAX) / Math.log(2)) + 1;
	private static final int SIZE = 1 << HEIGHT;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer tokens;
	
	private static int N;
	
	private static final int[] cnts = new int[MAX + 1];		//	cnts[i] : 맛이 i+1인 사탕의 개수
	private static final int[] segtree = new int[SIZE];	//	맛 범위에 따른 사탕 개수 누적합 segtree
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		for(int q = 0; q < N; q++) {
			tokens = new StringTokenizer(br.readLine());
			int cmd = Integer.parseInt(tokens.nextToken());
			
			switch(cmd) {
			case 1:
				int rank = Integer.parseInt(tokens.nextToken());	//	rank번째로 맛있는 사탕 뽑기
				sb.append(query(segtree, cnts, 1, 0, MAX, rank)).append("\n");
				
				break;
			case 2:
				int idx = Integer.parseInt(tokens.nextToken());		//	맛이 idx인 사탕을
				int diff = Integer.parseInt(tokens.nextToken());	//	abs(diff)개 만큼 넣거나 빼기
				
				update(segtree, cnts, 1, 0, MAX, idx - 1, diff);
			}
		}
		
		System.out.print(sb);
	} 	//	main-end
	
	//	node가 담당하는 [start, end] 범위에서 rank번째로 맛있는 사탕 1개 줄이고, 사탕 맛 반환
	private static int query(int[] segtree, int[] cnts, int node, int start, int end, int rank) {
		if(start == end) {	//	해당 사탕 찾은 경우
			update(segtree, cnts, 1, 0, MAX, start, -1);
			return start + 1;
		}
		
		if(rank <= segtree[2 * node])	//	rank번째 사탕이 왼쪽 subtree에 있을 경우
			return query(segtree, cnts, 2 * node, start, (start + end) / 2, rank);
		else	//	rank번째 사탕이 오른쪽 subtree에 있을 경우, 오른쪽 subtree에서 rank - (왼쪽 subtree 사탕 개수) 번째 사탕을 구해야 함
			return query(segtree, cnts, 2 * node + 1, (start + end) / 2 + 1, end, rank - segtree[2 * node]);
	}

	//	맛이 idx + 1인 사탕 개수를 diff만큼 변화시키기
	private static void update(int[] segtree, int[] cnts, int node, int start, int end, int idx, int diff) {
		if(idx < start || end < idx)	//	node가 담당하는 범위를 벗어날 경우
			return;
		
		if(start == end) {		//	idx 담당하는 단말 노드일 경우
			cnts[idx] += diff;	//	idx에 해당하는 사탕 개수 변화
			segtree[node] = cnts[idx];	//	segtree 단말 노드 값 변화
			return;
		}
		
		//	left subtree update
		update(segtree, cnts, 2 * node, start, (start + end) / 2, idx, diff);
		//	right subtree update
		update(segtree, cnts, 2 * node + 1, (start + end) / 2 + 1, end, idx, diff);
		
		//	left, right subtree로 node 업데이트
		segtree[node] = segtree[2 * node] + segtree[2 * node + 1];
	}
}	//	Main-class-end
```