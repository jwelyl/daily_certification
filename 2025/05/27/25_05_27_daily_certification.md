# 25_05_27_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 12906 새로운 하노이 탑

[12906번: 새로운 하노이 탑](http://boj.ma/12906/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashSet;
import java.util.LinkedList;
import java.util.Objects;
import java.util.Queue;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static String aStr = "";
	private static String bStr = "";
	private static String cStr = "";
	private static int aCnt = 0;
	private static int bCnt = 0;
	private static int cCnt = 0;
	
	private static final Set<Status> visited = new HashSet<>();
	private static Status target;
	private static final Queue<Node> queue = new LinkedList<>();
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		
		if(Integer.parseInt(st.nextToken()) > 0)
			aStr = st.nextToken();
		
		for(int i = 0; i < aStr.length(); i++) {
			char ch = aStr.charAt(i);
			
			if(ch == 'A')
				aCnt++;
			else if(ch == 'B')
				bCnt++;
			else
				cCnt++;
		}
		
		st = new StringTokenizer(br.readLine());
		
		if(Integer.parseInt(st.nextToken()) > 0)
			bStr = st.nextToken();
		
		for(int i = 0; i < bStr.length(); i++) {
			char ch = bStr.charAt(i);
			
			if(ch == 'A')
				aCnt++;
			else if(ch == 'B')
				bCnt++;
			else
				cCnt++;
		}
		
		st = new StringTokenizer(br.readLine());
		
		if(Integer.parseInt(st.nextToken()) > 0)
			cStr = st.nextToken();
		
		for(int i = 0; i < cStr.length(); i++) {
			char ch = cStr.charAt(i);
			
			if(ch == 'A')
				aCnt++;
			else if(ch == 'B')
				bCnt++;
			else
				cCnt++;
		}
		
		Status init = new Status(aStr, bStr, cStr);
		target = new Status(getStr('A', aCnt), getStr('B', bCnt), getStr('C', cCnt));
		
		Node node = new Node(init, 0);
		visited.add(init);
		
		if(!init.equals(target))
			queue.offer(node);
		
		System.out.println(bfs());
	} //	main-end

	private static int bfs() {
		while(!queue.isEmpty()) {
			Node cnode = queue.poll();
			Status cstatus = cnode.status;
			int ccnt = cnode.cnt;
			
			String nAStr = null;
			String nBStr = null;
			String nCStr = null;
			char ch = 0;
			
			//	1. A막대기에서 옮기기
			if(cstatus.aStr.length() > 0) {
				nAStr = cstatus.aStr.substring(0, cstatus.aStr.length() - 1);
				ch = cstatus.aStr.charAt(cstatus.aStr.length() - 1);
				
				//	1-1. B막대기로 옮기기
				nBStr = new StringBuilder().append(cstatus.bStr).append(ch).toString();
				nCStr = cstatus.cStr;
				
				Status nStatus1 = new Status(nAStr, nBStr, nCStr);
				if(nStatus1.equals(target))
					return ccnt + 1;
				
				if(!visited.contains(nStatus1)) {
					visited.add(nStatus1);
					queue.offer(new Node(nStatus1, ccnt + 1));
				}
				
				//	1-2. C막대기로 옮기기
				nBStr = cstatus.bStr;
				nCStr = new StringBuilder().append(cstatus.cStr).append(ch).toString();
				
				Status nStatus2 = new Status(nAStr, nBStr, nCStr);
				if(nStatus2.equals(target))
					return ccnt + 1;
				
				if(!visited.contains(nStatus2)) {
					visited.add(nStatus2);
					queue.offer(new Node(nStatus2, ccnt + 1));
				}
			}
			//	2. B막대기에서 옮기기
			if(cstatus.bStr.length() > 0) {
				nBStr = cstatus.bStr.substring(0, cstatus.bStr.length() - 1);
				ch = cstatus.bStr.charAt(cstatus.bStr.length() - 1);
				
				//	2-1. A막대기로 옮기기
				nAStr = new StringBuilder().append(cstatus.aStr).append(ch).toString();
				nCStr = cstatus.cStr;
				
				Status nStatus1 = new Status(nAStr, nBStr, nCStr);
				if(nStatus1.equals(target))
					return ccnt + 1;
				
				if(!visited.contains(nStatus1)) {
					visited.add(nStatus1);
					queue.offer(new Node(nStatus1, ccnt + 1));
				}
				
				//	2-2. C막대기로 옮기기
				nAStr = cstatus.aStr;
				nCStr = new StringBuilder().append(cstatus.cStr).append(ch).toString();
				
				Status nStatus2 = new Status(nAStr, nBStr, nCStr);
				if(nStatus2.equals(target))
					return ccnt + 1;
				
				if(!visited.contains(nStatus2)) {
					visited.add(nStatus2);
					queue.offer(new Node(nStatus2, ccnt + 1));
				}
				
			}
			//	3. C막대기에서 옮기기
			if(cstatus.cStr.length() > 0) {
				nCStr = cstatus.cStr.substring(0, cstatus.cStr.length() - 1);
				ch = cstatus.cStr.charAt(cstatus.cStr.length() - 1);	
				
				//	3-1. A막대기로 옮기기
				nAStr = new StringBuilder().append(cstatus.aStr).append(ch).toString();
				nBStr = cstatus.bStr;
				
				Status nStatus1 = new Status(nAStr, nBStr, nCStr);
				if(nStatus1.equals(target))
					return ccnt + 1;
				
				if(!visited.contains(nStatus1)) {
					visited.add(nStatus1);
					queue.offer(new Node(nStatus1, ccnt + 1));
				}
				
				//	3-2. B막대기로 옮기기
				nAStr = cstatus.aStr;
				nBStr = new StringBuilder().append(cstatus.bStr).append(ch).toString();
				
				Status nStatus2 = new Status(nAStr, nBStr, nCStr);
				if(nStatus2.equals(target))
					return ccnt + 1;
				
				if(!visited.contains(nStatus2)) {
					visited.add(nStatus2);
					queue.offer(new Node(nStatus2, ccnt + 1));
				}
			}	
		}
		
		return 0;
	}
	
	private static String getStr(char ch, int cnt) {
		StringBuilder sb = new StringBuilder();
		for(int i = 0; i < cnt; i++)
			sb.append(ch);
		
		return sb.toString();
	}
	
	private static class Status {
		public String aStr;
		public String bStr;
		public String cStr;
		
		public Status(String aStr, String bStr, String cStr) {
			this.aStr = aStr;
			this.bStr = bStr;
			this.cStr = cStr;
		}
		
		@Override
		public boolean equals(Object obj) {
			if(this == obj)
				return true;
			
			if(obj instanceof Status) {
				Status other = (Status)obj;
				return this.aStr.equals(other.aStr) && this.bStr.equals(other.bStr) && this.cStr.equals(other.cStr);
			}
			
			return false;
		}
		
		@Override
		public int hashCode() {
			return Objects.hash(this.aStr, this.bStr, this.cStr);
		}
	}
	
	private static class Node {
		public Status status;
		public int cnt;
		
		public Node(Status status, int cnt) {
			this.status = status;
			this.cnt = cnt;
		}
	}
} //	Main-class-end
```