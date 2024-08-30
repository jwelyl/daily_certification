# 24_08_30_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 10875 뱀

[](https://www.acmicpc.net/problem/10875)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final long NONE = Long.MAX_VALUE;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int L;	//	격자 x, y 좌표 범위 -L ~ L
	private static int N;	//	뱀 머리 회전 횟수
	
	private static int dir = 0;		//	현재 뱀이 바라보는 방향 (초기는 오른쪽)
	
	private static final List<Segment> snake = new ArrayList<>();
	
	private static long ans = NONE;
	
	public static void main(String[] args) throws IOException {
		L = Integer.parseInt(br.readLine());
		N = Integer.parseInt(br.readLine());
		
		Segment cur = new Segment();
		cur.x1 = 0;
		cur.y1 = 0;
		cur.time1 = 0;
		
		for(int i = 0; i < N; i++) {	
			st = new StringTokenizer(br.readLine());
			int addTime = Integer.parseInt(st.nextToken());
			char turn = st.nextToken().charAt(0);
			
			int x2 = cur.x1 + dx[dir] * addTime;
			int y2 = cur.y1 + dy[dir] * addTime;
			long time2 = cur.time1 + addTime;
			
			cur.x2 = x2;
			cur.y2 = y2;
			
			//	이전 선분들과 만나는지 확인
			//	cur과 바로 직전 선분은 꺾이는 지점에서 만나므로 두 번째 전 선분부터 가장 처음 선분까지 거꾸로 확인
			for(int j = snake.size() - 2; j >= 0; j--) {
				Segment prev = snake.get(j);
				long[] meet = meet(cur, prev);
				
				if(meet[0] == NONE || meet[1] == NONE)	//	cur과 prev는 교점이 없을 경우
					continue;
				
				ans = Math.min(ans, meet[2]);
			}
			
			if(ans != NONE) {
				System.out.println(ans);
				return;
			}
			
			if(x2 > L) {	//	다음 꺾기 전에 x 좌표가 벗어날 경우
				System.out.println(time2 - x2 + L + 1);
				return;
			}
			else if(x2 < -L) {
				System.out.println(time2 + L + 1 + x2);
				return;
			}
			else if(y2 > L) {	//	다음 꺾기 전에 y 좌표가 벗어날 경우
				System.out.println(time2 - y2 + L + 1);
				return;
			}
			else if(y2 < -L) {
				System.out.println(time2 + L + 1 + y2);
				return;
			}
			
			snake.add(cur);
			cur = new Segment();
			cur.x1 = x2;
			cur.y1 = y2;
			cur.time1 = time2;
			
			dir = nextDir(turn);
		}
		
		if(dir == 0) {
			cur.x2 = L + 1;
			cur.y2 = cur.y1;
		}
		else if(dir == 2) {
			cur.x2 = -L - 1;
			cur.y2 = cur.y1;
		}
		else if(dir == 1) {
			cur.x2 = cur.x1;
			cur.y2 = -L - 1;
		}
		else if(dir == 1) {
			cur.x2 = cur.x1;
			cur.y2 = L + 1;
		}

		//	이전 선분들과 만나는지 확인
		//	cur과 바로 직전 선분은 꺾이는 지점에서 만나므로 두 번째 전 선분부터 가장 처음 선분까지 거꾸로 확인
		for(int i = snake.size() - 2; i >= 0; i--) {
			Segment prev = snake.get(i);
			long[] meet = meet(cur, prev);
			
			if(meet[0] == NONE || meet[1] == NONE)	//	cur과 prev는 교점이 없을 경우
				continue;
			
			ans = Math.min(ans, meet[2]);
		}
		
		if(ans != NONE) {
			System.out.println(ans);
			return;
		}
		
		if(dir == 0) {
			System.out.println(L + 1 - cur.x1 + cur.time1);
		}
		else if(dir == 1)
			System.out.println(L + 1 + cur.y1 + cur.time1);
		else if(dir == 2)
			System.out.println(L + 1 + cur.x1 + cur.time1);
		else
			System.out.println(L + 1 - cur.y1 + cur.time1);
		
	}	//	main-end
	
	private static final int[] dx = {1, 0, -1, 0};
	private static final int[] dy = {0, -1, 0, 1};
	
	private static int nextDir(char turn) {
		switch(dir) {
		case 0:
			return turn == 'L' ? 3 : 1;
		case 1:
			return turn == 'L' ? 0 : 2;
		case 2:
			return turn == 'L' ? 1 : 3;
		default:
			return turn == 'L' ? 2 : 0;
		}
	}
	
	//	cur과 prev가 만나는 {x 좌표, y 좌표, 시각} 반환 
	private static long[] meet(Segment cur, Segment prev) {
		long[] ret = {NONE, NONE, NONE};
		
		int minC;
		int maxC;
		int minP;
		int maxP;
		
		if(cur.y1 == cur.y2 && prev.y1 == prev.y2) {	//	둘 다 수평선일 경우
			minC = Math.min(cur.x1, cur.x2);
			maxC = Math.max(cur.x1, cur.x2);
			minP = Math.min(prev.x1, prev.x2);
			maxP = Math.max(prev.x1, prev.x2);
			
			if(cur.y1 != prev.y1 || minP > maxC || maxP < minC)	//	두 선분이 겹치지 않을 경우
				return ret;
			
			ret[1] = cur.y1;
			if(minC <= minP && minP <= maxC) {
				ret[0] = minP;
				ret[2] = cur.time1 + Math.abs(minP - cur.x1);
			}
			else {
				ret[0] = minC;
				ret[2] = cur.time1 + Math.abs(minC - cur.x1);
			}	
		}
		else if(cur.x1 == cur.x2 && prev.x1 == prev.x2) {	//	둘 다 수직선일 경우
			minC = Math.min(cur.y1, cur.y2);
			maxC = Math.max(cur.y1, cur.y2);
			minP = Math.min(prev.y1, prev.y2);
			maxP = Math.max(prev.y1, prev.y2);
			
			if(cur.x1 != prev.x1 || minP > maxC || minC > maxP)	//	두 선분이 겹치지 않을 경우
				return ret;
			
			ret[0] = cur.x1;
			if(minC <= minP && minP <= maxC) {
				ret[1] = minP;
				ret[2] = cur.time1 + Math.abs(minP - cur.y1);
			}
			else {
				ret[1] = minC;
				ret[2] = cur.time1 + Math.abs(minC - cur.y1);
			}	
		}
		else if(cur.y1 == cur.y2 && prev.x1 == prev.x2) {	//	cur은 수평선, prev는 수직선일 경우
			minC = Math.min(cur.x1, cur.x2);
			maxC = Math.max(cur.x1, cur.x2);
			minP = Math.min(prev.y1, prev.y2);
			maxP = Math.max(prev.y1, prev.y2);
			
			if(cur.y1 > maxP || cur.y1 < minP || prev.x1 > maxC || prev.x1 < minC)
				return ret;
			
			ret[0] = prev.x1;
			ret[1] = cur.y1;
			ret[2] = cur.time1 + Math.abs(prev.x1 - cur.x1);
		}
		else {
			minC = Math.min(cur.y1, cur.y2);
			maxC = Math.max(cur.y1, cur.y2);
			minP = Math.min(prev.x1, prev.x2);
			maxP = Math.max(prev.x1, prev.x2);
			
			if(cur.x1 > maxP || cur.x1 < minP || prev.y1 > maxC || prev.y1 < minC)
				return ret;
			
			ret[0] = cur.x1;
			ret[1] = prev.y1;
			ret[2] = cur.time1 + Math.abs(prev.y1 - cur.y1);
		}
		
		return ret;
	}
	
	private static class Segment {
		public int x1;
		public int y1;		//	꺾인 후 처음 출발 좌표
		public long time1;	//	꺽인 시간
		public int x2;
		public int y2;		//	다음 꺾이는 순간 좌표
	}
}	//	Main-class-end
```