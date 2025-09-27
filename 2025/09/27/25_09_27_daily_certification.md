# 25_09_27_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 10852 Carpets

[10852번: Carpets](http://boj.ma/10852/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;

    private static int W;
    private static int H;	//	바닥 가로, 세로 크기
    private static int C;	//	카펫 개수
    
    private static boolean[][] covered;
    
    private static final List<int[]> carpets = new ArrayList<>();
    
    private static int sum = 0;
    
    public static void main(String[] args) throws IOException {
    	st = new StringTokenizer(br.readLine());
    	W = Integer.parseInt(st.nextToken());
    	H = Integer.parseInt(st.nextToken());
    	C = Integer.parseInt(br.readLine());
    	
    	covered = new boolean[H][W];
    	
    	for(int c = 0; c < C; c++) {
    		st = new StringTokenizer(br.readLine());
        	int a = Integer.parseInt(st.nextToken());
        	int w = Integer.parseInt(st.nextToken());
        	int h = Integer.parseInt(st.nextToken());
        	
        	sum += w * h * a;
        	
        	for(int i = 0; i < a; i++)
        		carpets.add(new int[] {h, w});
    	}
    	
    	if(sum < W * H) {	//	모든 카펫 넓이 합해도 바닥 넓이만 못할 경우
    		System.out.println("no");
    		return;
    	}
    	
    	permutation(0, new int[carpets.size()], new boolean[carpets.size()]);
    	
    	System.out.println("no");
    }    //    main-end

    private static void dfs(int carpetNth, int posNth, int[] order, int remain) {
    	//	카펫을 다 썼거나, 모든 칸을 다 확인한 경우
    	if(carpetNth == carpets.size() || posNth == H * W)
    		return;
    	
    	int y = posNth / W;
    	int x = posNth % W;
    	
    	if(covered[y][x])	//	(y, x)칸이 이미 덮힌 경우, 다음 칸 확인
    		dfs(carpetNth, posNth + 1, order, remain);
    	else {
    		int[] carpet = carpets.get(order[carpetNth]);	//	이번에 덮어볼 카펫
    		int h = carpet[0];
    		int w = carpet[1];
    		
    		if(canCover(y, x, h, w)) {		//	회전하지 않은 상태로 덮을 수 있을 경우
    			cover(y, x, h, w, true);	//	회전하지 않은 상태로 덮기
    			if(remain - h * w == 0) {	//	덮었을 때 빈칸이 없을 경우
    				System.out.println("yes");
    				System.exit(0);
    			}
    			
    			dfs(carpetNth + 1, posNth + 1, order, remain - h * w);	//	덮은 상태로 다음 카펫, 다음 칸 확인
    			
    			cover(y, x, h, w, false);	//	덮은 것 복구
    		}
    		
    		if(canCover(y, x, w, h)) {		//	회전한 상태로 덮을 수 있을 경우
    			cover(y, x, w, h, true);	//	회전한 상태로 덮기
    			if(remain - h * w == 0) {	//	덮었을 때 빈칸이 없을 경우
    				System.out.println("yes");
    				System.exit(0);
    			}
    			
    			dfs(carpetNth + 1, posNth + 1, order, remain - h * w);	//	덮은 상태로 다음 카펫, 다음 칸 확인
    			
    			cover(y, x, w, h, false);
    		}
    		
    		//	회전 여부와 상관없이 덮을 수 없다면, 이 카펫 순서는 잘못된 것임
    	}
    }
    
    //	(y, x)를 좌상단으로 h * w 면적을 덮을 수 있는지 확인
    private static boolean canCover(int y, int x, int h, int w) {
    	for(int yy = y; yy < y + h; yy++) {
    		for(int xx = x; xx < x + w; xx++) {
    			if(!isIn(yy, xx) || covered[yy][xx])
    				return false;
    		}
    	}
    	
    	return true;
    }
    
    //	(y, x)를 좌상단으로 h * w 면적을 status 상태로 변경
    private static void cover(int y, int x, int h, int w, boolean status) {
    	for(int yy = y; yy < y + h; yy++) {
    		for(int xx = x; xx < x + w; xx++)
    			covered[yy][xx] = status;
    	}
    }
    
    private static void permutation(int nth, int[] order, boolean[] used) {
    	if(nth == carpets.size()) {	//	카펫 덮을 순서 모두 정한 경우
    		dfs(0, 0, order, H * W);	//	정해진 순서대로 덮기 시도
    		return;
    	}
    	
    	for(int i = 0;i < carpets.size(); i++) {
    		if(!used[i]) {
    			used[i] = true;
    			order[nth] = i;
    			permutation(nth + 1, order, used);
    			used[i] = false;
    		}
    	}
    }
    
    private static boolean isIn(int y, int x) {
    	return (0 <= y && y < H) && (0 <= x && x < W);
    }
}    //    Main-class-end
```

### BOJ 17178 줄서기

[](https://www.acmicpc.net/problem/17178)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.PriorityQueue;
import java.util.Deque;
import java.util.ArrayDeque;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    줄 수
    private static Ticket[] tickets;
    private static final PriorityQueue<Ticket> pq = new PriorityQueue<>();    //    입장 순서 나타내는 pq
    private static final Deque<Ticket> stack = new ArrayDeque<>();            //    대기 스택
    private static int order = 0;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        tickets = new Ticket[N * 5];
        for(int i = 0; i < N; i++) {
            st = new StringTokenizer(br.readLine());
            for(int j = 0; j < 5; j++) {
                tickets[i * 5 + j] = new Ticket(st.nextToken());
                pq.offer(tickets[i * 5 + j]);
            }
        }
        
        while(!pq.isEmpty()) {    //    아직 입장해야 할 티켓이 남은 경우
            Ticket now = pq.peek();    //    현재 입장해야 할 티켓

            if(order < N * 5) {    //    아직 밖에서 대기하고 있는 티켓이 있을 경우
                Ticket out = tickets[order];    //    현재 밖에서 대기 중인 티켓 중 가장 빠른 순서
                
                if(out == now) {    //    지금 들어갈 타이밍인 경우
                    order++;
                    pq.poll();      //    입장함
                }
                else {
                    if(!stack.isEmpty()) {    //    대기열도 확인해보기
                        Ticket wait = stack.peekFirst();    //    가장 마지막에 대기열에 들어간 티켓
                        
                        if(now == wait) {            //    마침 들어갈 타이밍일 경우
                            stack.pollFirst();       //    대기열에서 꺼냄
                            pq.poll();               //    입장함
                            continue;
                        }
                    }
                    //    밖에서 대기하고 있던 티켓을 대기열에 넣기
                    stack.offerFirst(out);
                    order++;
                }
            }
            else {    //    밖에서 대기하고 있는 티켓은 없을 경우
                if(!stack.isEmpty()) {
                    Ticket wait = stack.peekFirst();    //    가장 마지막에 대기열에 들어간 티켓

                    if(now == wait) {            //    마침 들어갈 타이밍일 경우
                        stack.pollFirst();       //    대기열에서 꺼냄
                        pq.poll();               //    입장함
                    }
                    else {
                        System.out.println("BAD");    //    순서가 꼬임
                        return;
                    }
                }
            }
        }
        
        System.out.println("GOOD");    //    모두 입장한 경우
    }    //    main-end
    
    private static class Ticket implements Comparable<Ticket> {
        public char alph;
        public int num;
        
        public Ticket(String ticket) {
            StringTokenizer st = new StringTokenizer(ticket, "-");
            this.alph = st.nextToken().charAt(0);
            this.num = Integer.parseInt(st.nextToken());
        }
        
        @Override
        public int compareTo(Ticket other) {
            //    알파벳 빠른 순으로
            int res = Integer.compare(this.alph, other.alph);
            
            if(res == 0)    //    알파벳이 같다면
                res = Integer.compare(this.num, other.num);    //    숫자가 작은 순서로
            
            return res;
        }
    }
}    //    Main-class-end
```