# 25_10_03_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 13146 같은 수로 만들기 2

[1933번: 스카이라인](http://boj.ma/1933/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.Deque;
import java.util.ArrayDeque;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	
	private static int N;
	private static final Deque<Integer> stack = new ArrayDeque<>();
	
	private static int maxHeight = 0;
	
	private static long answer = 0;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		for(int i = 0; i < N; i++) {
			int height = Integer.parseInt(br.readLine());
			maxHeight = Math.max(maxHeight, height);
			
			if(!stack.isEmpty()) {
				int prevHeight = stack.peekLast();
				if(prevHeight < height)
					answer += (height - prevHeight);
			}
			
			stack.offerLast(height);
		}
		
		answer += maxHeight - stack.peekLast();
		
		System.out.println(answer);
	} //	main-end
} //	Main-class-end
```

### BOJ 5464 주차장

[5464번: 주차장](http://boj.ma/5464/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.ArrayDeque;
import java.util.PriorityQueue;

public class Main {
    private static final int NONE = 0;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    주차 공간 수
    private static int M;    //    차량 수
    
    private static int[] fees;        //    fees[i] : i번째 주차 공간의 무게 당 요금
    private static int[] weights;     //    weights[i] : i번째 차량 무게
    private static int[] orders;      //    입/출차 순서
    private static int[] parks;       //    parks[i] : i번째 차량이 주차한 공간
    
    //    바로 주차하지 못하고 대기하는 차량 대기열
    private static final Queue<Integer> waitQueue = new ArrayDeque<>();
    //    주차 가능한 주차 공간 번호
    private static final Queue<Integer> pq = new PriorityQueue<>();
    
    private static int answer = 0;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        fees = new int[N + 1];
        weights = new int[M + 1];
        orders = new int[2 * M + 1];
        parks = new int[M + 1];
        
        for(int i = 1; i <= N; i++) {
            fees[i] = Integer.parseInt(br.readLine());
            pq.offer(i);
        }
        for(int i = 1; i <= M; i++)
            weights[i] = Integer.parseInt(br.readLine());
        for(int i = 1; i <= 2 * M; i++)
            orders[i] = Integer.parseInt(br.readLine());
        
        for(int i = 1; i <= 2 * M; i++) {
            int car = orders[i];
            
            if(car > 0) {    //    입차일 경우
                if(pq.isEmpty()) {    //    주차 가능한 공간이 없을 경우
                    waitQueue.offer(car);    //    대기열에 넣기
                    continue;
                }
                else
                    parks[car] = pq.poll();
            }
            else {    //    출차일 경우
                int park = parks[-car];    //    이번에 출차하는 주차 공간
                answer += weights[-car] * fees[park];
                pq.offer(park);    //    주차 공간 반납
                
                if(!waitQueue.isEmpty()) {    //    주차 공간이 없어서 대기열에 있던 차가 있을 경우
                    int wait = waitQueue.poll();
                    parks[wait] = pq.poll();
                }
            }
        }
        
        System.out.println(answer);
    }    //    main-end
}    //    Main-class-end
```