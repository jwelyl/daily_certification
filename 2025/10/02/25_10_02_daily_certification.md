# 25_10_02_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1933 스카이라인

[1933번: 스카이라인](http://boj.ma/1933/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.PriorityQueue;
import java.util.StringTokenizer;
import java.util.TreeMap;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int N;	//	빌딩 수
	//	빌딩 x 좌표 순, 
	private static final PriorityQueue<Building> pq = new PriorityQueue<>();
	private static final TreeMap<Integer, Integer> heightMap = new TreeMap<>(Collections.reverseOrder());
	
	private static int prev;
	private static final List<Building> answer = new ArrayList<>();
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		for(int i = 0; i < N; i++) {
			st = new StringTokenizer(br.readLine());
			int leftX = Integer.parseInt(st.nextToken());
			int height = Integer.parseInt(st.nextToken());
			int rightX = Integer.parseInt(st.nextToken());
			
			pq.offer(new Building(leftX, height));
			pq.offer(new Building(rightX, -height));
		}
		
		while(!pq.isEmpty()) {
			Building now = pq.poll();
			
			if(now.height > 0)	//	왼쪽 좌표, 새로운 빌딩 시작일 경우
				heightMap.put(now.height, heightMap.getOrDefault(now.height, 0) + 1);
			else {	//	오른쪽 좌표, 어떤 빌딩의 끝일 경우
				int height = -now.height;
				int cnt = heightMap.get(height);
				
				//	해당 건물 제거
				if(cnt == 1)
					heightMap.remove(height);
				else
					heightMap.put(height, cnt - 1);
			}
			
			if(heightMap.isEmpty()) {
				answer.add(new Building(now.x, 0));
				continue;
			}
			
			answer.add(new Building(now.x, heightMap.firstKey()));
		}
		
		sb.append(answer.get(0)).append(" ");
		prev = answer.get(0).height;
		
		for(int i = 0; i < answer.size(); i++) {
			if(prev != answer.get(i).height) {
				sb.append(answer.get(i)).append(" ");
				prev = answer.get(i).height;
			}
 		}
		
		System.out.println(sb);
	} //	main-end
	
	private static class Building implements Comparable<Building> {
		public int x;		//	빌딩의 x 좌표
		public int height;		//	빌딩의 높이 (음수일 경우, 빌딩의 오른쪽 x 좌표임)
		
		public Building(int x, int height) {
			this.x = x;
			this.height = height;
		}
		
		@Override
		public int compareTo(Building other) {
			//	빌딩의 x 좌표로 오름차순 정렬
			int res = Integer.compare(this.x, other.x);
			
			if(res == 0)	//	빌딩의 x 좌표가 같을 경우
				res = Integer.compare(other.height, this.height);	//	빌딩 높이 순으로 내림차순 정렬, 자연스레 오른쪽 x 좌표는 뒤로 밀림
			
			return res;
		}
		
		@Override
		public String toString() {
			return new StringBuilder().append(this.x).append(" ").append(this.height).toString();
		}
	}
} //	Main-class-end
```

### BOJ 14622 소수 게임

[14622번: 소수 게임](http://boj.ma/14622/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.PriorityQueue;
import java.util.Set;
import java.util.HashSet;
import java.util.Collections;

public class Main {
    private static final int MAX = 5_000_000;
    private static final int SCORE = 1_000;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    //    SIEVE[num] : num이 소수일 경우 false
    private static final boolean[] SIEVE = new boolean[MAX + 1];
    
    //    실제로 나온 소수 집합
    private static final Set<Integer> usedSet = new HashSet<>();
    //    대웅이가 말한 소수 리스트 (크기 순)
    private static final PriorityQueue<Integer> pq1 = new PriorityQueue<>(Collections.reverseOrder());    
    //    규성이가 말한 소수 리스트 (크기 순)
    private static final PriorityQueue<Integer> pq2 = new PriorityQueue<>(Collections.reverseOrder());
    
    private static long score1 = 0;    //    대웅 점수
    private static long score2 = 0;    //    규성 점수
    
    public static void main(String[] args) throws IOException {
        init();
        
        N = Integer.parseInt(br.readLine());
        for(int round = 1; round <= N; round++) {
            st = new StringTokenizer(br.readLine());
            int num1 = Integer.parseInt(st.nextToken());    //    대웅이가 수를 먼저 말함
            int num2 = Integer.parseInt(st.nextToken());    //    그 다음에 규성이가 수를 말함
            
            if(SIEVE[num1]) {    //    대웅이가 말한 수가 소수가 아닐 경우
                if(pq2.size() >= 3) {
                    int p1 = pq2.poll();
                    int p2 = pq2.poll();
                    int p3 = pq2.poll();
                    
                    score2 += p3;
                    
                    pq2.offer(p1);
                    pq2.offer(p2);
                    pq2.offer(p3);
                }
                else
                    score2 += SCORE;
            }
            else if(usedSet.contains(num1))    //    대웅이가 이미 말한 소수를 말할 경우
                score1 -= SCORE;
            else {
                pq1.offer(num1);
                usedSet.add(num1);
            }
            
            if(SIEVE[num2]) {    //    규성이가 말한 수가 소수가 아닐 경우
                if(pq1.size() >= 3) {
                    int p1 = pq1.poll();
                    int p2 = pq1.poll();
                    int p3 = pq1.poll();
                    
                    score1 += p3;
                    
                    pq1.offer(p1);
                    pq1.offer(p2);
                    pq1.offer(p3);
                }
                else
                    score1 += SCORE;
            }
            else if(usedSet.contains(num2))    //    규성이가 이미 말한 소수를 말할 경우
                score2 -= SCORE;
            else {
                pq2.offer(num2);
                usedSet.add(num2);
            }
        }
        
        System.out.println(score1 > score2 ? "소수의 신 갓대웅" : (score1 == score2 ? "우열을 가릴 수 없음" : "소수 마스터 갓규성"));
    }    //    main-end
    
    private static void init() {
        SIEVE[0] = true;
        SIEVE[1] = true;
        
        for(int num = 2; num * num <= MAX; num++) {
            if(!SIEVE[num]) {    //    num이 소수일경우
                for(int mult = num * num; mult <= MAX; mult += num)
                    SIEVE[mult] = true;
            }
        }
    }
}    //    Main-class-end
```