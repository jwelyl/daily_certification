# 24_11_23_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ** 8983 사냥꾼

[8983번: 사냥꾼](https://boj.ma/8983/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int M;	//	사대 개수
	private static int N;	//	동물 개수
	private static int L;	//	사정거리
	
	private static int[] pos;	//	사대 위치
	
	private static int ans = 0;	//	사냥 가능한 동물의 수
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		M = Integer.parseInt(st.nextToken());
		N = Integer.parseInt(st.nextToken());
		L = Integer.parseInt(st.nextToken());
		
		pos = new int[M];
		st = new StringTokenizer(br.readLine());
		for (int i = 0; i < M; i++)
			pos[i] = Integer.parseInt(st.nextToken());
		
		Arrays.sort(pos);	//	사대 위치로 정렬
		
		for (int i = 0; i < N; i++) {
			st = new StringTokenizer(br.readLine());
			int x = Integer.parseInt(st.nextToken());
			int y = Integer.parseInt(st.nextToken());	//	동물 위치
			
			if (y > L)	//	모든 사대의 y 좌표는 0이므로 y > L일 경우 어떤 사대에서도 사냥 불가
				continue;
			
			//	상대 높이는 y로 고정됨
			//	x 좌표 차가 가장 작은 사대와의 x 좌표 차이
			int minXDist = getMinXDistance(x);
			
			if (minXDist + y <= L)
				ans++;
		}
		
		System.out.println(ans);
	}	//	main-end
	
	private static int getMinXDistance(int target) {
		int lower = getNearestPosition(target, false);	//	target보다 작거나 같은 값
		int upper = getNearestPosition(target, true);	//	target보다 크거나 같은 값
		
		if (lower == -1)	//	target보다 작거나 같은 좌표가 없을 경우 
			return Math.abs(target - upper);
		if (upper == -1)	//	target보다 크거나 같은 좌표가 없을 경우 
			return Math.abs(target - lower);
		
		return Math.min(Math.abs(target - lower), Math.abs(target - upper));
	}
	
	private static int getNearestPosition(int target, boolean findUpper) {
		int start = 0;
		int end = M - 1;
		int res = -1;
		
		while (start <= end) {
			int mid = (start + end) / 2;
			
			if (pos[mid] == target)	//	mid의 좌표가 target과 일치할 경우
				return pos[mid];	//	그대로 반환
			if (pos[mid] < target) {	//	mid의 좌표가 target보다 작을 경우
				if (!findUpper)	//	target보다 더 작은 값을 찾아야 하는 경우
					res = pos[mid];	//	일단 저장
				start = mid + 1;	//	target보다는 작은, 더 큰 좌표 찾아보기
			} else {	//	mid의 좌표가 target보다 클 경우
				if (findUpper)	//	target보다 더 큰 값을 찾아야 하는 경우
					res = pos[mid];
				end = mid - 1;	//	target보다는 큰, 더 작은 좌표 찾아보기
			}
		}
		
		return res;
	}
}	//	Main-class-end
```