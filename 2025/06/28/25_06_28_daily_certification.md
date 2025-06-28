# 25_06_28_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2478 자물쇠

[2478번: 자물쇠](http://boj.ma/2478/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	//	shiftStatus[i] : [1, 2, 3, ..., N]에서 i-왼쪽밀기 했을때의 상태 
	private static int[][] shiftStatus;
	private static int[] inputStatus;	//	입력 상태이자 최종 상태
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		shiftStatus = new int[N][N];
		inputStatus = new int[N];
		
		st = new StringTokenizer(br.readLine());
		for(int i = 0; i < N; i++)
			inputStatus[i] = Integer.parseInt(st.nextToken());
		
		for(int i = 0; i < N; i++) {
			int num = i + 1;

			for(int j = 0; j < N; j++) {
				shiftStatus[i][j] = num % N == 0 ? N : num % N;
				num++;
			}
		}
		
		for(int k2 = 1; k2 < N; k2++) {
			int[] rShifted = shiftRight(k2);	//	입력 상태에서 오른쪽으로 k2만큼 민 상태, 즉 초기 상태에서 k1만큼 왼쪽으로 밀고, [p, q] 뒤집은 상태의 후보
			
			for(int k1 = 1; k1 < N; k1++) {
				int[] lShifted = shiftStatus[k1];	//	초기 상태에서 k1만큼 왼쪽으로 민 상태
				
				int p = 0;
				int q = N - 1;	//	뒤집을 구간 
				
				while(p < N && rShifted[p] == lShifted[p])
					p++;
				while(q >= 0 && rShifted[q] == lShifted[q])
					q--;
				
				flip(lShifted, p, q);	//	초기 상태에서 k1만큼 왼쪽으로 민 배열을 구간 [p, q]를 뒤집음
				
				if(isSame(lShifted, rShifted)) {	//	정답을 찾은 경우
					System.out.println(k1);
					System.out.println((p + 1) + " " + (q + 1));
					System.out.println(k2);
					return;
				}
				else	//	원상복구
					flip(lShifted, p, q);
			}
		}
	} //	main-end
	
	private static boolean isSame(int[] arr1, int[] arr2) {
		for(int i = 0; i < N; i++) {
			if(arr1[i] != arr2[i])
				return false;
		}
		
		return true;
	}
	
	private static void flip(int[] arr, int p, int q) {
		int idx1 = p;
		int idx2 = q;
		
		while(idx1 <= idx2) {
			int tmp = arr[idx1];
			arr[idx1] = arr[idx2];
			arr[idx2] = tmp;
			
			idx1++;
			idx2--;
		}
	}
	
	private static int[] shiftRight(int k) {
		int[] res = new int[N];
		
		for(int i = N - k; i < N; i++)
			res[i - N + k] = inputStatus[i];
		for(int i = 0; i < N - k; i++)
			res[i + k] = inputStatus[i];
		
		return res;
	}
} //	Main-class-end
```