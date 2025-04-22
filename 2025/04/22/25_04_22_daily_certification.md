# 25_04_22_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1797 **균형잡힌 줄서기**

[1797번: 균형잡힌 줄서기](http://boj.ma/1797/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;
	
	private static final List<Person> list = new ArrayList<>();
	
	private static int[] prefixSumF;
	private static int[] prefixSumM;
	
	//	Key : prefixSumF[i] - prefixSumM[i] 값, Value : 이때 가능한 i의 최솟값
	private static final Map<Integer, Integer> minMap = new HashMap<>();
	
	private static int answer = Integer.MIN_VALUE;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		list.add(new Person(0, Integer.MIN_VALUE));
		minMap.put(0, 0);
		
		for(int i = 1; i <= N; i++) {
			st = new StringTokenizer(br.readLine());
			list.add(new Person(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken())));
		}
		
		Collections.sort(list);
		prefixSumF = new int[N + 1];
		prefixSumM = new int[N + 1];
		for(int i = 1; i <= N; i++) {
			Person p = list.get(i);
			prefixSumF[i] = prefixSumF[i - 1] + (p.sex == 1 ? 1 : 0);
			prefixSumM[i] = prefixSumM[i - 1] + (p.sex == 1 ? 0 : 1);
			
			int diff = prefixSumF[i] - prefixSumM[i];
			int minIdx = minMap.getOrDefault(diff, Integer.MAX_VALUE);
			if(i < minIdx)
				minMap.put(diff, i);
		}
	
		for(int i = 1; i <= N; i++) {
			Person now = list.get(i);
			int diff = prefixSumF[i] - prefixSumM[i];
			int nowPos = now.pos;
			
			int minIdx = minMap.get(diff);
			
			if(i == minIdx)
				continue;
			
			Person prev = list.get(minIdx + 1);
			int prevPos = prev.pos;
			
			answer = Math.max(answer, nowPos - prevPos);
		}
		
		System.out.println(answer);
	} //	main-end
	
	private static class Person implements Comparable<Person> {
		public int sex;
		public int pos;
		
		public Person(int sex, int pos) {
			this.sex = sex;
			this.pos = pos;
		}
		
		@Override
		public int compareTo(Person other) {
			return Integer.compare(this.pos, other.pos);
		}
	}
} //	Main-class-end
```