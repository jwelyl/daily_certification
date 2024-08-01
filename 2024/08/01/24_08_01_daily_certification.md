# 24_08_01_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 23309 철도 공사

[](https://www.acmicpc.net/problem/23309)

**시간 초과 (객체 매번 생성)**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX = 1_000_001;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    
    private static final CDLL cdll = new CDLL();
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++) {
        	int num = Integer.parseInt(st.nextToken());
        	
        	cdll.insertBack(num);
        }
        
        for(int i = 0; i < M; i++) {
        	st = new StringTokenizer(br.readLine());
        	
        	String cmd = st.nextToken();
        	
        	if(cmd.equals("BN") || cmd.equals("BP")) {
        		int num1 = Integer.parseInt(st.nextToken());
        		int num2 = Integer.parseInt(st.nextToken());
        		
        		if(cmd.equals("BN"))
        			sb.append(cdll.insertNext(num1, num2)).append("\n");
        		else
        			sb.append(cdll.insertPrev(num1, num2)).append("\n");
        	}
        	else {
        		int num = Integer.parseInt(st.nextToken());
        		
        		if(cmd.equals("CN"))
        			sb.append(cdll.deleteNext(num)).append("\n");
        		else
        			sb.append(cdll.deletePrev(num)).append("\n");
        	}
        }
        
        System.out.print(sb);
    }   //  main-end
    
    private static class Node {
        public int num;
        public Node prev;
        public Node next;
        
        public Node(int num) {
            this.num = num;
            this.prev = null;
            this.next = null;
        }
    }
    
    private static class CDLL {
        public Node head;
        public Node tail;
        public Node[] nodeArr;	//	nodeArr[num] : 고유 번호가 num인 node
        
        public CDLL() {
            this.head = new Node(-1);
            this.tail = new Node(-1);
            
            this.head.next = this.tail;
            this.tail.prev = this.head;
            
            this.nodeArr = new Node[MAX];
            for(int num = 0; num < MAX; num++)
            	this.nodeArr[num] = new Node(num); 
        }
        
        public void insertBack(int num) {
            Node node = this.nodeArr[num];
            
            Node prev = this.tail.prev;
            
            prev.next = node;
            node.prev = prev;
            node.next = this.tail;
            this.tail.prev = node;
        }
        
        public int insertNext(int cnum, int nnum) {
            Node cnode = this.nodeArr[cnum];
            Node nnode = this.nodeArr[nnum];
            
            int ret = (cnode.next == this.tail ? this.head.next : cnode.next).num;
            
            Node next = cnode.next;
            cnode.next = nnode;
            nnode.prev = cnode;
            nnode.next = next;
            next.prev = nnode;
            
            return ret;
        }
        
        public int insertPrev(int cnum, int pnum) {
        	Node cnode = this.nodeArr[cnum];
            Node pnode = this.nodeArr[pnum];
            
            int ret = (cnode.prev == this.head ? this.tail.prev : cnode.prev).num;
            
            Node prev = cnode.prev;
            cnode.prev = pnode;
            pnode.next = cnode;
            prev.next = pnode;
            pnode.prev = prev;
            
            return ret;
        }
        
        public int deleteNext(int num) {
            Node node = this.nodeArr[num];
            Node del = node.next == this.tail ? this.head.next : node.next;
            
            Node prev = del.prev;
            Node next = del.next;
            prev.next = next;
            next.prev = prev;
            
            return del.num;
        }
        
        public int deletePrev(int num) {
        	Node node = this.nodeArr[num];
            Node del = node.prev == this.head ? this.tail.prev : node.prev;
            
            Node prev = del.prev;
            Node next = del.next;
            prev.next = next;
            next.prev = prev;
            
            return del.num;
        }
    }
}   //  Main-class-end
```

**성공 코드 (prev, next int 배열로 이전 노드, 다음 노드 관리)**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX = 1_000_001;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    
    private static final int[] prev = new int[MAX];	//	prev[num] : 고유 번호가 num인 노드의 이전 노드 고유 번호
    private static final int[] next = new int[MAX];	//	next[num] : 고유 번호가 num인 노드의 다음 노드 고유 번호
    
    private static int[] inits;	//	초기 존재 역
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        inits = new int[N];
        
        st = new StringTokenizer(br.readLine());
        
        int input = Integer.parseInt(st.nextToken());
        inits[0] = input;
        
        for(int i = 1; i < N; i++) {
        	input = Integer.parseInt(st.nextToken());
        	
        	inits[i] = input;
        	prev[inits[i]] = inits[i - 1];	//	i번째 노드의 이전 노드는 i-1번째 노드
        	next[inits[i - 1]] = inits[i];	//	i-1번째 노드의 다음 노드는 i번째 노드

        }
        
        next[inits[N - 1]] = inits[0];	//	마지막 노드의 다음 노드는 첫번째 노드
        prev[inits[0]] = inits[N - 1];	//	첫번째 노드의 이전 노드는 마지막 노드
        
        for(int i = 0; i < M; i++) {
        	st = new StringTokenizer(br.readLine());
        	
        	String cmd = st.nextToken();
        	int ret = 0;
        	
        	if(cmd.equals("BN") || cmd.equals("BP")) {
        		int num1 = Integer.parseInt(st.nextToken());
        		int num2 = Integer.parseInt(st.nextToken());
        		
        		if(cmd.equals("BN")) {
        			ret = next[num1];	//	num1의 현재 다음 노드
        			
        			next[num1] = num2;
        			prev[num2] = num1;	//	num1의 뒤에 num2 넣기
        			
        			next[num2] = ret;
        			prev[ret] = num2;	//	num2의 뒤에 ret 넣기
        		}	
        		else {
        			ret = prev[num1];	//	num1의 현재 이전 노드
        			
        			prev[num1] = num2;
        			next[num2] = num1;	//	num1의 앞에 num2 넣기
        			
        			prev[num2] = ret;
        			next[ret] = num2;	//	num2의 앞에 ret 넣기
        		}
        	}
        	else {
        		int num = Integer.parseInt(st.nextToken());
        		
        		if(cmd.equals("CN")) {
        			ret = next[num];	//	num의 현재 다음 노드
        			
        			next[num] = next[ret];
        			prev[next[ret]] = num;	//	num 뒤에 ret의 다음 노드 연결 
        		}
        		else {
        			ret = prev[num];	//	num의 현재 이전 노드
        			
        			prev[num] = prev[ret];
        			next[prev[ret]] = num;	//	num 앞에 ret의 이전 노드 연결
        		}
        	}
        	
        	sb.append(ret).append("\n");
        }
        
        System.out.print(sb);
    }   //  main-end
}   //  Main-class-end
```