# 25_05_26_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 23326 홍익 투어리스트

[23326번: 홍익 투어리스트](http://boj.ma/23326/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.TreeSet;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;
	private static int Q;
	
	private static final TreeSet<Integer> set = new TreeSet<>();
	
	private static int cur = 0;	//	현재 위치
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		Q = Integer.parseInt(st.nextToken());
		
		st = new StringTokenizer(br.readLine());
		for(int i = 0; i < N; i++) {
			int pos = Integer.parseInt(st.nextToken());
			if(pos == 1)
				set.add(i);
		}
		
		for(int q = 0; q < Q; q++) {
			st = new StringTokenizer(br.readLine());
			int opt = Integer.parseInt(st.nextToken());
			
			if(opt == 1) {	//	pos가 명소였으면 명소에서 해제, 아니면 명소로 지정
				int pos = Integer.parseInt(st.nextToken()) - 1;
				if(set.contains(pos))
					set.remove(pos);
				else
					set.add(pos);
			}
			else if(opt == 2) {	//	현재 위치에서 시계방향으로 x만큼 이동
				int x = Integer.parseInt(st.nextToken());
				cur = (cur + x) % N;
			}
			else {
				if(set.isEmpty())	//	명소가 존재하지 않을 경우
					sb.append("-1\n");
				else {
					Integer left = set.first();
					Integer right = set.ceiling(cur);
					
					if(right == null)	//	오른쪽에 명소가 없는 경우, N번째 장소를 반환해서 다시 가야함
						sb.append(N - cur + left).append("\n");
					else
						sb.append(right - cur).append("\n");
				}
			}
		}
		
		System.out.print(sb);
	} //	main-end
} //	Main-class-end
```