# 25_07_16_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 15883 First In Last Out

[15883번: First In Last Out](http://boj.ma/15883/t)

```java
public class Main {
	private static final int H0 = 1;
	private static final int H1 = H0 * 16;
	private static final int H2 = H1 * 16;
	private static final int H3 = H2 * 16;
	private static final int H4 = H3 * 16;
	
	private static int counter = 0;
	
	private static final StringBuilder sb = new StringBuilder();
	
	public static void main(String[] args) {
		backtracking(0, new boolean[16], new int[6]);
		
		System.out.print(sb);
	}	//	main-end
	
	private static void backtracking(int nth, boolean[] used, int[] selected) {
		if(nth == 6) {
			int num1 = H0 * selected[3] + H1 * selected[2] + H2 * selected[1] + H3 * selected[0];
			int num2 = H0 * selected[5] + H1 * selected[0] + H2 * selected[1] + H3 * selected[4];
			int sum = num1 + num2;
			
			int d0 = sum / H4;
			int d1 = (sum % H4) / H3;
			int d2 = (sum % H3) / H2;
			int d3 = (sum % H2) / H1;
			int d4 = sum % H1;
			
			boolean[] resUsed = new boolean[16];
			resUsed[d0] = true;
			if(resUsed[d1])
				return;
			resUsed[d1] = true;
			if(resUsed[d2])
				return;
			resUsed[d2] = true;
			if(resUsed[d3])
				return;
			resUsed[d3] = true;
			if(resUsed[d4])
				return;
			resUsed[d4] = true;
			
			if(d0 == selected[2] && d1 == selected[3] && !used[d2] && !used[d3] && !used[d4]) {
				StringBuilder res = new StringBuilder();
				res.append(Integer.toHexString(num1));
				res.append(" + ");
				res.append(Integer.toHexString(num2));
				res.append(" = ");
				res.append(Integer.toHexString(sum));
				
				sb.append(res.toString()).append("\n");
			}
			
			// 주기적으로 GC 요청
			if (++counter % 100_000 == 0)
				Runtime.getRuntime().gc();
			
			return;
		}
		
		for(int num = (nth % 2 == 0) ? 1 : 0; num < 16; num++) {
			if(!used[num]) {
				used[num] = true;
				selected[nth] = num;
				backtracking(nth + 1, used, selected);
				used[num] = false;
			}
		}
	}
}	//	Main-class-end
```

憼順