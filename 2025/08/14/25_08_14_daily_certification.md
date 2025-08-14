# 25_08_14_daily_certification

# Problem Solving (Algorithm & SQL)

### **Codetree 여왕 개미**

[Codetree | Learning to Code with Confidence](https://www.codetree.ai/ko/frequent-problems/samsung-sw/problems/queen-ant/description)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int INIT = 100;			//	마을 건설
	private static final int CREATE = 200;			//	개미집 건설
	private static final int DELETE = 300;			//	개미집 철거
	private static final int PATROL = 400;			//	개미집 정찰
	
	private static final int NONE = -1;				//	철거된 집
	private static final int MAX = 10_000;			
	private static final int INF = 1_000_000_000;	//	최대 좌표
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int Q;	//	쿼리 개수
	private static int N;	//	최초 개미집 개수
	
	private static int idx = 1;
	//	numToPos[num] : num번째 개미집의 x 좌표, 철거되었으면 NONE
	private static final int[] numToPos = new int[2 * MAX + 1];
	
	public static void main(String[] args) throws IOException {
		Q = Integer.parseInt(br.readLine());
		
		for(int q = 0; q < Q; q++) {
			st = new StringTokenizer(br.readLine());
			
			int cmd = Integer.parseInt(st.nextToken());
			
			switch(cmd) {
			case INIT:
				init();
				break;
			case CREATE:
				create();
				break;
			case DELETE:
				delete();
				break;
			case PATROL:
				patrol();
			}
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static void init() {
		N = Integer.parseInt(st.nextToken());
		
		for(; idx <= N; idx++)
			numToPos[idx] = Integer.parseInt(st.nextToken());
	}
	
	private static void create() {
		numToPos[idx++] = Integer.parseInt(st.nextToken());
	}
	
	private static void delete() {
		int q = Integer.parseInt(st.nextToken());
		numToPos[q] = NONE;
	}
	
	private static void patrol() {
		int r = Integer.parseInt(st.nextToken());

		int start = 0;
		int end = INF;	//	최악의 경우 개미 한마리가 0부터 INF까지 전부 정찰해야 함
		
		//	r마리의 개미가 각각 맡은 구간 정찰할때 각각의 개미의 정찰 시간 중 최대 시간의 최솟값
		int res = end - start;
		
		while(start <= end) {
			int mid = (start + end) / 2;	//	정찰에 가장 오래 걸리는 개미도 mid 이하의 시간에 정찰 가능해야 함
			
			if(ok(mid, r)) {	//	정찰에 가장 오래 걸리는 개미도 mid 이하의 시간이 걸릴 경우
				res = mid;
				end = mid - 1;
			}
			else
				start = mid + 1;
		}
		
		sb.append(res).append("\n");
	}
	
	private static boolean ok(int limit, int r) {
		int startIdx = 0;	//	철거되지 않은 개미집 중 가장 작은 집 번호 (여왕개미집 제외)
		int startPos = 0;	//	현재 개미 출발 위치
		
		int cnt = 1;	//	필요한 개미 마리수
		
		for(int i = 1; i < idx; i++) {
			if(numToPos[i] != NONE) {	//	철거되지 않은 집 중 가장 앞에 있는 집 찾음
				startPos = numToPos[i];
				startIdx = i;
				break;
			}
		}
		
		for(int i = startIdx; i < idx; i++) {
			if(numToPos[i] == NONE)	//	철거된 집일 경우 무시
				continue;
			
			if(numToPos[i] - startPos > limit) {
				cnt++;
				startPos = numToPos[i];
			}
		}
		
		return cnt <= r;	//	모든 개미가 limit 이하 시간만 정찰할 때 r마리보다 많은 개미가 필요하면 최대 정찰 시간 limit을 키워야 함 
	}
}	//	Main-class-end
```