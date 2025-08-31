# 25_08_31_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2836 수상 택시

[2836번: 수상 택시](http://boj.ma/2836/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Collections;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;	//	수상 택시 탈 사람
	private static int M;	//	도착지
	
	private static final List<Reverse> reverseList = new ArrayList<>();

    private static int start;
    private static int end;        //    역주행 구간
    
    private static long answer = 0;
	
	public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        for(int i = 0; i < N; i++) {
            st = new StringTokenizer(br.readLine());
            int from = Integer.parseInt(st.nextToken());
            int to = Integer.parseInt(st.nextToken());
            
            if(from > to)    //    역주행할 경우
                reverseList.add(new Reverse(from, to));
        }
        
        //    사람이 아예 없거나, 있어도 역주행하는 사람이 없을 경우
        if(reverseList.size() == 0) {
            System.out.println(M);
            return;
        }
        
        Collections.sort(reverseList);
        
        start = reverseList.get(0).to;
        end = reverseList.get(0).from;
        answer += start;

        for(int i = 1; i < reverseList.size(); i++) {
            Reverse reverse = reverseList.get(i);
            int s = reverse.to;
            int e = reverse.from;
            
            if(s <= end)    //    기존 역주행 구간과 겹칠 경우
                end = Math.max(end, e);    //    역주행 구간 최대 길이 갱신
            else {    //    새로운 역주행 구간 생길 경우
                answer += 2 * (end - start);      //    기존 역주행 구간 왕복 비용 (start -> end, end -> start)
                answer += (s - start);            //    새로운 역주행 구간까지 이동
                
                start = s;
                end = e;        //    새로운 역주행 구간 시작
            }
        }
        
        answer += 2 * (end - start);      //    마지막 역주행 구간 왕복 비용 (start -> end, end -> start)
        answer += (M - start);            //    M까지 이동 비용

        System.out.println(answer);
	} //	main-end
	
	private static class Reverse implements Comparable<Reverse> {
		public int from;	//	역주행하는 사람의 출발지
		public int to;		//	역주행하는 사람의 도착지 (to < from)
		
        public Reverse(int from, int to) {
            this.from = from;
            this.to = to;
        }
        
        @Override
        public int compareTo(Reverse other) {
            int res = Integer.compare(this.to, other.to);        //    도착지가 작은 순으로 정렬
            
            if(res == 0)    //    도착지가 같을 경우
                res = Integer.compare(this.from, other.from);    //    출발지가 작은 순으로 정렬
            
            return res;
        }
	}
} //	Main-class-end
```

### BOJ 1464 뒤집기 3

[1464번: 뒤집기 3](http://boj.ma/1464/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Deque;
import java.util.LinkedList;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

	private static char[] input;
	private static final Deque<Character> deque = new LinkedList<>();
	
	public static void main(String[] args) throws IOException {
		input = br.readLine().toCharArray();
		
		for(int i = 0; i < input.length; i++) {
			if(!deque.isEmpty() && deque.peekLast() < input[i])
				deque.offerFirst(input[i]);
			else
				deque.offerLast(input[i]);
		}
		
		while(!deque.isEmpty())
			System.out.print(deque.pollLast());
		System.out.println();
	} //	main-end
} //	Main-class-end
```