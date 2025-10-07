# 25_10_07_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2065 나룻배

[2065번: 나룻배](http://boj.ma/2065/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.ArrayDeque;

public class Main {
    private static final int LEFT = 0;
    private static final int RIGHT = 1;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int M;    //    나룻배에 태울 수 있는 손님 수
    private static int T;    //    나룻배가 강을 건너는 시간
    private static int N;    //    손님 수
    
    //    왼쪽 정박장
    private static final Queue<Passenger> leftQueue = new ArrayDeque<>();
    //    오른쪽 정박장
    private static final Queue<Passenger> rightQueue = new ArrayDeque<>();
    
    private static int time = 0;
    private static int pos = LEFT;      //    나룻배 위치
    private static int[] arrived;    //    arrived[id] : id 승객이 자신의 목적지에 도착하는 시간
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        M = Integer.parseInt(st.nextToken());
        T = Integer.parseInt(st.nextToken());
        N = Integer.parseInt(st.nextToken());
        
        arrived = new int[N + 1];
        
        for(int id = 1; id <= N; id++) {
            st = new StringTokenizer(br.readLine());
            int time = Integer.parseInt(st.nextToken());
            Passenger passenger = new Passenger(id, time);
            
            if(st.nextToken().equals("left"))
                leftQueue.offer(passenger);
            else
                rightQueue.offer(passenger);
        }
      
        //	남은 승객이 있는 경우
        while(!leftQueue.isEmpty() || !rightQueue.isEmpty()) {
        	int arrivalTime = Integer.MAX_VALUE;	//	왼쪽, 오른쪽 상관 없이 가장 빠르게 도착하는 승객의 도착 시간
        	
        	if(!leftQueue.isEmpty())
        		arrivalTime = Math.min(arrivalTime, leftQueue.peek().time);
        	if(!rightQueue.isEmpty())
        		arrivalTime = Math.min(arrivalTime, rightQueue.peek().time);
        	
        	//	당장 태울 수 있는 승객이 없을 경우
        	if((leftQueue.isEmpty() || leftQueue.peek().time > time) && (rightQueue.isEmpty() || rightQueue.peek().time > time))
        		time = arrivalTime;	//	가장 빨리 오는 승객 올때까지 기다리기
        	
        	int cnt = M;	//	현재 배에 태울 수 있는 승객 수
        	
        	if(pos == LEFT) {
        		while(!leftQueue.isEmpty() && leftQueue.peek().time <= time && cnt > 0) {
        			Passenger passenger = leftQueue.poll();
        			arrived[passenger.id] = time + T;
        			cnt--;
        		}
        		
        		if(cnt < M) {	//	왼쪽에서 한명이라도 태운 경우
        			pos = RIGHT;
        			time += T;		//	오른쪽으로 이동
        		}
        		else if(!rightQueue.isEmpty() && rightQueue.peek().time <= time) {	//	오른쪽에서 기다리는 손님 있을 경우
        			pos = RIGHT;
        			time += T;
        		}
        	}
        	else {
        		while(!rightQueue.isEmpty() && rightQueue.peek().time <= time && cnt > 0) {
        			Passenger passenger = rightQueue.poll();
        			arrived[passenger.id] = time + T;
        			cnt--;
        		}
        		
        		if(cnt < M) {	//	오른쪽에서 한명이라도 태운 경우
        			pos = LEFT;
        			time += T;		//	왼쪽으로 이동
        		}
        		else if(!leftQueue.isEmpty() && leftQueue.peek().time <= time) {	//	왼쪽에서 기다리는 손님 있을 경우
        			pos = LEFT;
        			time += T;
        		}
        	}
        }
        
        for(int id = 1; id <= N; id++)
            sb.append(arrived[id]).append("\n");
        
        System.out.print(sb);
    }    //    main-end
    
    private static class Passenger {
        public final int id;        //    승객 id
        public final int time;      //    정박장 도착하는 시간
        
        public Passenger(int id, int time) {
            this.id = id;
            this.time = time;
        }
    }
}    //    Main-class-end
```