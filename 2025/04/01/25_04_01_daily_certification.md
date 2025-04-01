# 25_04_01_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 5430 AC

[5430번: AC](http://boj.ma/5430/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int T;                //    테스트 케이스 개수
    private static char[] cmd;           //    수행할 명령어
    private static int N;                //    배열에 들어있는 수 개수
    
    private static DLL dq = null;        //    deque
    
    public static void main(String[] args) throws IOException {
        T = Integer.parseInt(br.readLine());
        
        for(int tc = 1; tc <= T; tc++) {
            dq = new DLL();
            cmd = br.readLine().toCharArray();
            
            N = Integer.parseInt(br.readLine());
            
            String input = br.readLine();
            input = input.length() > 2 ? input.substring(1, input.length() - 1) : "";
            
            st = new StringTokenizer(input, ",");
            
            for(int i = 0; i < N; i++)
                dq.pushBack(Integer.parseInt(st.nextToken()));
            
            boolean error = false;
            
            for(char opt : cmd) {
                if(opt == 'R')
                    dq.flip();
                else {
                    if(!dq.pop()) {
                        error = true;
                        break;
                    }
                }
            }
            
            if(error)
                sb.append("error\n");
            else
                dq.print();
        }
        
        System.out.print(sb);
    }    //    main-end
    
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
    
    private static class DLL {
        public Node head;
        public Node tail;
        public boolean flipped;
        
        public DLL() {
            this.head = new Node(Integer.MIN_VALUE);
            this.tail = new Node(Integer.MAX_VALUE);
            
            this.head.next = this.tail;
            this.tail.prev = this.head;
            this.flipped = false;
        }
        
        public void pushBack(int num) {
            Node node = new Node(num);
            this.tail.prev.next = node;
            node.prev = this.tail.prev;
            this.tail.prev = node;
            node.next = this.tail;
        }
        
        private boolean isEmpty() {
            return this.head.next == this.tail && this.tail.prev == this.head;
        }
        
        public void flip() {
            this.flipped = !this.flipped;
        }
        
        public boolean pop() {
            if(this.isEmpty())    //    덱이 비었을 경우
                return false;
            
            if(flipped)    //    뒤집혔을 경우
                this.popBack();
            else
                this.popFront();
            
            return true;
        }
        
        private void popFront() {
            Node delNode = this.head.next;
            Node nNext = delNode.next;
            
            nNext.prev = this.head;
            this.head.next = nNext;
        }
        
        private void popBack() {
            Node delNode = this.tail.prev;
            Node nPrev = delNode.prev;
            
            nPrev.next = this.tail;
            this.tail.prev = nPrev;
        }
        
        public void print() {
            Node cur = null;
            sb.append("[");
            
            if(flipped) {    //    뒤집힌 경우 역순으로
                cur = this.tail.prev;
                
                while(cur != this.head) {
                    sb.append(cur.num);
                    
                    if(cur.prev != this.head)
                        sb.append(",");
                    
                    cur = cur.prev;
                }
            }
            else {    //    뒤집히지 않은 경우 정방향으로
                cur = this.head.next;
                
                while(cur != this.tail) {
                    sb.append(cur.num);
                    
                    if(cur.next != this.tail)
                        sb.append(",");
                    
                    cur = cur.next;
                }
            }
            
            sb.append("]\n");
        }
    }
}    //    Main-class-end
```

### BOJ 14715 전생했더니 슬라임 연구자였던 건에 대하여 (Easy)

[14715번: 전생했더니 슬라임 연구자였던 건에 대하여 (Easy)](http://boj.ma/14715/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	
	private static int K;
	private static int cnt = 0;	//	K의 소인수 개수
	
    public static void main(String[] args) throws IOException {
    	K = Integer.parseInt(br.readLine());
    	
    	int d = 2;
    	while(K != 1) {
    		while(K % d == 0) {	//	d로 K를 최대한 나눠보기
    			cnt++;
    			K /= d;
    		}
    		
    		d++;
    	}
    	
    	System.out.println((int)Math.ceil(Math.log(cnt) / Math.log(2)));
    }    //    main-end
}    //    Main-class-end
```