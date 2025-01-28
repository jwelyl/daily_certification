# 25_01_28_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 1561 놀이 공원**

[1561번: 놀이 공원](https://boj.ma/1561/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;	//	아이 수
	private static int M;	//	놀이기구 수
	
	private static int[] times;			//	times[i] : i번째 놀이기구 운행시간
	private static int maxTime = 0;		//	M개 놀이기구의 운행시간 중 가장 긴 운행시간

	public static void main(String[] args) throws Exception {
	    st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		times = new int[M];
		st = new StringTokenizer(br.readLine());
		for(int i = 0; i < M; i++) {
			times[i] = Integer.parseInt(st.nextToken());
			maxTime = Math.max(maxTime, times[i]);
		}

		parametricSearch();
	}	//	main-end
	
	private static void parametricSearch() {
		if(N <= M) {	//	아이 수보다 놀이기구 수가 더 많아서 N명이 동시에 탑승할 수 있을 경우
			System.out.println(N);
			return;
		}
		
		long start = 0;
		long end = 1L * N * maxTime;
		long res = end;				//	N명이 모두 탑승할 수 있게 되는 최소 시간
		
		while(start <= end) {
			long mid = (start + end) / 2;
			
			if(canRide(mid) >= N) {		//	mid 시간 안에 N명이 모두 탑승할 수 있을 경우
				res = mid;				//	일단 mid 저장
				end = mid - 1;			//	시간 더 줄여보기
			}
			else						//	mid 시간 안에 N명이 모두 탑승할 수 없을 경우
				start = mid + 1;		//	시간 더 늘려보기
		}
		
		//	res가 되서야 N번째 아이가 간신히 탑승할 수 있음
		//	res - 1일때 몇명이 탑승한 상태인지, 그리고 다 타고 내린 상태인지 확인
		long cnt = N - canRide(res - 1);	//	res - 1 시간동안 탑승하고 이제 더 타야 하는 아이 수
	
		for(int i = 0; i < M; i++) {
			if(res % times[i] == 0) {	//	i번째 놀이기구에 새로 탑승할 수 있을 경우
				cnt--;
				
				if(cnt == 0) {	//	마지막 아이가 탑승한 경우
					System.out.println(i + 1);
					return;
				}
			}
		}
	}
	
	//	time 동안 탑승할 수 있는 아이 수
	private static long canRide(long time) {
		long cnt = 0;
		
		for(int i = 0; i < M; i++) {
			cnt += 1;						//	일단 0초에 각 놀이기구 별로 한명씩 기본으로 탑승
			cnt += (time / times[i]);		//	이후 time 동안 i번째 놀이기구에 탈 수 있는 아이 수
		}
		
		return cnt;
	}
}	//	Main-class-end
```