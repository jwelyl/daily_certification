# 25_04_12_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16434 드래곤 앤 던전

[16434번: 드래곤 앤 던전](http://boj.ma/16434/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;
	private static int hAtk;	//	용사의 초기 공격력
	
	private static long[][] rooms;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		
		N = Integer.parseInt(st.nextToken());
		hAtk = Integer.parseInt(st.nextToken());
	
		rooms = new long[N][3];
		for(int i = 0; i < N; i++) {
			st = new StringTokenizer(br.readLine());
			int t = Integer.parseInt(st.nextToken());
			int a = Integer.parseInt(st.nextToken());
			int h = Integer.parseInt(st.nextToken());
		
			rooms[i][0] = t;
			rooms[i][1] = a;
			rooms[i][2] = h;
		}
		
		System.out.println(parametricSearch());
	}	//	main-end
	
	private static long parametricSearch() {
		long start = 1;
		long end = 1_000_000_000_000_000_000L;
		long res = 1_000_000_000_000_000_000L;
		
		while(start <= end) {
			long mid = start + (end - start) / 2;	//	최대 체력을 mid라 가정
			
			if(ok(mid)) {	//	최대 체력이 mid일때 지나갈 수 있을 경우
				res = mid;
				end = mid - 1;		//	더 적은 체력으로 지나갈 수 있나 체크
			}
			else
				start = mid + 1;	//	더 많은 체력으로 지나갈 수 있나 체크
		}
		
		return res;
	}
	
	private static boolean ok(long maxHp) {
		long curHp = maxHp;	//	현재 체력
		long curAtk = hAtk;	//	현재 공격력
		
		int pos = 0;
		
		while(pos < N) {
			long t = rooms[pos][0];
			long a = rooms[pos][1];
			long h = rooms[pos][2];
			
			if(t == 1) {	//	몬스터일경우
				long cnt1 = h / curAtk + (h % curAtk != 0 ? 1 : 0);	//	용사가 몬스터를 없애기 위해 공격해야 할 횟수
				long cnt2 = curHp / a + (curHp % a != 0 ? 1 : 0);	//	몬스터가 용사를 없애기 위해 공격해야 할 횟수
				
				if(cnt1 > cnt2)	//	용사가 더 많이 때려야 할 경우
					return false;
				
				curHp -= a * (cnt1 - 1);	//	몬스터가 cnt1번째 때리지 못하고 죽음
			}
			else {	//	포션일 경우
				curHp = curHp + h > maxHp ? maxHp : curHp + h;
				curAtk += a;
			}
			
			pos++;	//	다음 방으로 이동
		}
		
		return true;	//	최대 체력 maxHp로 통과 가능
	}
}	//	Main-class-end
```