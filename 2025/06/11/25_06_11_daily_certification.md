# 25_06_11_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 12764 싸지방에 간 준하

[http://boj.ma/12764/t](http://boj.ma/12764/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Map;
import java.util.PriorityQueue;
import java.util.StringTokenizer;
import java.util.TreeMap;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;	//	사람 수
	
	private static final List<Person> personList = new ArrayList<>();
	
	private static final PriorityQueue<PC> busyPq = new PriorityQueue<>(); 				// 끝나는 시간 기준
	private static final PriorityQueue<Integer> availablePq = new PriorityQueue<>(); 	// PC 번호 기준
	
	private static int pcCount = 0;
	//	Key : 컴퓨터 번호, Value : 해당 컴퓨터 이용한 사람 수
	private static final Map<Integer, Integer> pcMap = new TreeMap<>();
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		for(int p = 0; p < N; p++) {
			st = new StringTokenizer(br.readLine());
			personList.add(new Person(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken())));
		}
		
		Collections.sort(personList);
		
		for (Person person : personList) {
		    // 현재 시간까지 사용 가능한 PC들 꺼내기
		    while (!busyPq.isEmpty() && busyPq.peek().end <= person.start)
		        availablePq.offer(busyPq.poll().pcNum);	//	사용 가능한 PC들의 pcNum 넣기

		    int assignedPcNum;
		    if (!availablePq.isEmpty()) {
		        assignedPcNum = availablePq.poll(); // 번호 가장 작은 사용 가능한 PC
		        pcMap.put(assignedPcNum, pcMap.get(assignedPcNum) + 1);
		    } else {	//	사용 가능한 pc가 없을 경우
		        pcCount++;
		        assignedPcNum = pcCount;
		        pcMap.put(assignedPcNum, 1);
		    }

		    busyPq.offer(new PC(assignedPcNum, person.end));
		}

		
		sb.append(pcMap.size()).append("\n");
		for(int key : pcMap.keySet())
			sb.append(pcMap.get(key)).append(" ");
		
		System.out.println(sb);
	}	//	main-end
	
	private static class PC implements Comparable<PC> {
		public int pcNum;
		public int end;			//	자리 나는 시간
		
		public PC(int pcNum, int end) {
			this.pcNum = pcNum;
			this.end = end;
		}

		@Override
		public int compareTo(PC other) {
			int res = Integer.compare(this.end, other.end);		//	자리 나는 시간이 빠른 PC부터
			
			if(res == 0)	//	자리 나는 시간이 같을 경우
				res = Integer.compare(this.pcNum, other.pcNum);	//	번호가 빠른 PC부터
			
			return res;
		}
	}
	
	private static class Person implements Comparable<Person> {
		public int start;	//	컴퓨터 이용 시작 시각
		public int end;		//	컴퓨터 이용 시작 시각
		
		public Person(int start, int end) {
			this.start = start;
			this.end = end;
		}
		
		@Override
		public int compareTo(Person other) {
			return Integer.compare(this.start, other.start);
		}
	}
}	//	Main-end
```