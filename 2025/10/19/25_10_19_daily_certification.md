# 25_10_19_daily_certification

# Problem Solving (Algorithm & SQL)

### 프로그래머스 가장 큰 수

[](https://school.programmers.co.kr/learn/courses/30/lessons/42746#)

```java
import java.util.List;
import java.util.ArrayList;
import java.util.Comparator;

class Solution {
    private int cnt0 = 0;
    
    public String solution(int[] numbers) {
        String answer = "";
        
        List<String> list = new ArrayList<>();
        for(int num : numbers) {
            list.add(Integer.toString(num));
            if(num == 0)
                cnt0++;
        }
        
        if(cnt0 == list.size())
            return "0";
        
        list.sort((str1, str2) -> (str2 + str1).compareTo(str1 + str2));
        
        for(String str : list)
            answer += str;
        
        return answer;
    }
}
```

### BOJ 16496 큰 수 만들기

[16496번: 큰 수 만들기](http://boj.ma/16496/t)

**정해?**

```jsx
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;
	private static final List<String> numList = new ArrayList<>();
	private static int cnt0;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		st = new StringTokenizer(br.readLine());
		for(int i = 0; i < N; i++) {
			String num = st.nextToken();
			if(num.equals("0"))
				cnt0++;
			numList.add(num);
		}
		
		//	모든 수가 0일 경우
		if(cnt0 == N)
			sb.append("0");
		else {
			numList.sort((num1, num2) -> (num2 + num1).compareTo(num1 + num2));
			for(String num : numList)
				sb.append(num);
		}
		
		System.out.println(sb);
	}	//	main-end
}	//	Main-class-end
```

**커스텀 정렬**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;			//	수의 개수
	private static final List<Num> numList = new ArrayList<>();	//	수 배열	

	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		st = new StringTokenizer(br.readLine());
		for(int i = 0; i < N; i++)
			numList.add(new Num(Integer.parseInt(st.nextToken())));
		
		Collections.sort(numList);

		for(int i = 0; i < N; i++) {
			int value = numList.get(i).value;
			sb.append(numList.get(i).value);
			if(i == 0 && value == 0)
				break;
		}
		
		System.out.println(sb);
	} //	main-end
	
	private static class Num implements Comparable<Num> {
		public int value;	//	수의 값 (0 ~ 1_000_000_000 사이 정수)
		public int len;	//	수의 자릿수
		public long expand;	//	확장된 수
		
		public Num(int value) {
			this.value = value;
			
			if(value == 0)	//	예외
				this.len = 1;
			else {
				this.len = (int)Math.log10(this.value) + 1;
			}
			
			int q = 10 / len;
			int r = ((q + 1) * len) % 10;
			
			StringBuilder tmp = new StringBuilder();
			
			for(int i = 0; i < q; i++)
				tmp = tmp.append(this.value);
			
			if(r != 0 && r != len) {
				tmp = tmp.append(this.value);
				tmp = tmp.delete(tmp.length() - r, tmp.length());
			}
			
			expand = Long.parseLong(tmp.toString());
		}
		
		@Override
		public int compareTo(Num num) {
			return Long.compare(num.expand, this.expand);
		}
	}	//	Num-class-end
} //	Main-class-end
```