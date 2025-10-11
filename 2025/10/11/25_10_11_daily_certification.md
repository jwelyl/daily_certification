# 25_10_11_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 31719 UDP 스택

[31719번: UDP 스택](http://boj.ma/31719/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Deque;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int T;    //    테스트케이스 개수
    private static int N;    //    수열 길이
    
    private static int[] sequence;

    private static final Deque<Integer>[] deques = new ArrayDeque[2];
    
    public static void main(String[] args) throws IOException {
    	T = Integer.parseInt(br.readLine());
    	
    	deques[0] = new ArrayDeque<>();
    	deques[1] = new ArrayDeque<>();
    	
    	for(int tc = 1; tc <= T; tc++) {
    		int now = 1;	//	오름차순 정렬할 때 현재 와야 할 수
    		int idx = 1;
    		
    		N = Integer.parseInt(br.readLine());
    		sequence = new int[N + 1];
    		
    		deques[0].clear();
    		deques[1].clear();	//	각 스택 초기화
    		
    		st = new StringTokenizer(br.readLine());
    		for(int i = 1; i <= N; i++)
    			sequence[i] = Integer.parseInt(st.nextToken());
    		 
    		while(now <= N) {	//	모든 수 정렬될때까지
    			if(!deques[0].isEmpty() && deques[0].peekFirst() == now) {	//	stack1에서 가져올 수 있는 수 가져오기
    				while(!deques[0].isEmpty()) {
    					deques[0].pollFirst();
    					now++;
    				}
    				
    				if(!deques[1].isEmpty() && deques[1].peekFirst() == now) {	//	stack2에서 가져올 수 있는 수 가져오기
    					while(!deques[1].isEmpty()) {
    						deques[1].pollFirst();
    						now++;
    					}
    				}
    			}
    			else if(!deques[1].isEmpty() && deques[1].peekFirst() == now) {	//	stack2에서 가져올 수 있는 수 가져오기
					while(!deques[1].isEmpty()) {
						deques[1].pollFirst();
						now++;
					}
					
					if(!deques[0].isEmpty() && deques[0].peekFirst() == now) {	//	stack1에서 가져올 수 있는 수 가져오기
	    				while(!deques[0].isEmpty()) {
	    					deques[0].pollFirst();
	    					now++;
	    				}
					}
				}

    			if(idx > N)	//	수열의 마지막 수까지 확인했고, 스택에서 가져올 수 있는 만큼 가져왔음
    				break;
    			
    			int num = sequence[idx];
    			
    			if(now == num) {	//	스택 저장할 필요 없을 경우
    				idx++;
    				now++;
    			}
    			else {	//	스택에 저장해야 할 경우
    				if(deques[0].isEmpty() && deques[1].isEmpty())	//	스택이 둘 다 비었을 경우
    					deques[0].offerLast(num);	//	아무 스택에나 넣기
    				else if(deques[0].isEmpty() && !deques[1].isEmpty()) {	//	둘 중 하나는 비었을 경우
    					if(deques[1].peekLast() == num - 1)	//	비어있지 않은 스택의 마지막 수가 num - 1일 경우
    						deques[1].offerLast(num);
    					else								//	비어있지 않은 스택에 넣을 수 없을 경우
    						deques[0].offerLast(num);
    				}
    				else if(deques[1].isEmpty() && !deques[0].isEmpty()) {	//	둘 중 하나는 비었을 경우
    					if(deques[0].peekLast() == num - 1)	//	비어있지 않은 스택의 마지막 수가 num - 1일 경우
    						deques[0].offerLast(num);
    					else								//	비어있지 않은 스택에 넣을 수 없을 경우
    						deques[1].offerLast(num);
    				}
    				else {
    					int num1 = deques[0].peekLast();
    					int num2 = deques[1].peekLast();
    					
    					if(num1 == num - 1)
    						deques[0].offerLast(num);
    					else if(num2 == num - 1)
    						deques[1].offerLast(num);
    					else	//	어디에도 넣을 수 없을 경우
    						break;
    				}
    				
    				idx++;
    			}
    		}
    		
    		sb.append(now == N + 1 ? "YES\n" : "NO\n");
    	}
    	
    	System.out.print(sb);
    }    //    main-end
}    //    Main-class-end
```