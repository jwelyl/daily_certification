# 25_06_06_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16134 조합 (Combination)

[16134번: 조합 (Combination)](http://boj.ma/16134/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX = 1_000_000;
	private static final int MOD = 1_000_000_007;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int R;
	
	private static final long[] fact = new long[MAX + 1];
	
	private static long denominator;
	private static long inv;
	private static long answer;
	
	public static void main(String[] args) throws IOException {
		init();
		
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		R = Integer.parseInt(st.nextToken());
		
		denominator = (fact[R] * fact[N - R]) % MOD;
		inv = pow(denominator, MOD - 2);
		answer = (fact[N] * inv) % MOD;
		
		System.out.println(answer);
	}	//	main-end
	
	private static void init() {
		fact[0] = 1;
		
		for(int i = 1; i <= MAX; i++)
			fact[i]= (fact[i - 1] * i) % MOD;
	}
	
	private static long pow(long num, int exp) {
		if(exp == 0)
			return 1;
		if(exp == 1)
			return num;
		
		long res = pow(num, exp >> 1);
		res = (res * res) % MOD;
		
		if((exp & 1) == 1)
			res = (res * num) % MOD;
	
		return res;
	}
}	//	Main-class-end
```