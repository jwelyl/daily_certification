# 25_02_21_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 14476 최대공약수 하나 빼기

[14476번: 최대공약수 하나 빼기](http://boj.ma/14476/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int[] nums;
	private static int[] prefixGCD;
	private static int[] suffixGCD;
	
	private static int maxGCD = -1;
	private static int maxGCDExcept;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());

		nums = new int[N];
		prefixGCD = new int[N];
		suffixGCD = new int[N];
		
		st = new StringTokenizer(br.readLine());
		for(int i = 0; i < N; i++)
			nums[i] = Integer.parseInt(st.nextToken());
		
		prefixGCD[0] = nums[0];
		suffixGCD[N - 1] = nums[N - 1];
		
		for(int i = 1; i < N; i++) {
			prefixGCD[i] = gcd(prefixGCD[i - 1], nums[i]);
			suffixGCD[N - 1 - i] = gcd(suffixGCD[N - i], nums[N - 1 - i]);
		}
		
		int gcd = suffixGCD[1];
		int except = nums[0];
		
		if(except % gcd != 0 && gcd > maxGCD) {
			maxGCD = gcd;
			maxGCDExcept = except;
		}
		
		gcd = prefixGCD[N - 2];
		except = nums[N - 1];
		
		if(except % gcd != 0 && gcd > maxGCD) {
			maxGCD = gcd;
			maxGCDExcept = except;
		}
		
		for(int i = 1; i < N - 1; i++) {
			except = nums[i];
			gcd = gcd(prefixGCD[i - 1], suffixGCD[i + 1]);
			
			if(except % gcd != 0 && gcd > maxGCD) {
				maxGCD = gcd;
				maxGCDExcept = except;
			}
		}
		
		System.out.print(maxGCD);
		if(maxGCD != -1)
			System.out.println(" " + maxGCDExcept);
	}	//	main-end
	
	private static int gcd(int num1, int num2) {
		int bigger = num1 > num2 ? num1 : num2;
		int smaller = num1 == bigger ? num2 : num1;
		
		while(true) {
			int r = bigger % smaller;
		
			if(r == 0)
				break;
			
			bigger = smaller;
			smaller = r;
		}
		
		return smaller;
	}
}	//	Main-class-end
```