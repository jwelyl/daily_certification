# 25_10_05_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 3830 **교수님은 기다리지 않는다**

[3830번: 교수님은 기다리지 않는다](http://boj.ma/3830/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int N;
	private static int M;
	
	private static Node[] nodeArr;
	
	public static void main(String[] args) throws IOException {
		while (true) {
			st = new StringTokenizer(br.readLine());
			N = Integer.parseInt(st.nextToken());
			M = Integer.parseInt(st.nextToken());

			if (N == 0 && M == 0) {
				System.out.println(sb);
				break;
			}
			
			nodeArr = new Node[N + 1];
			for(int v = 1; v <= N; v++)
				nodeArr[v] = new Node(v, v, 0);
			
			for(int q = 0; q < M; q++) {
				st = new StringTokenizer(br.readLine());
				char cmd = st.nextToken().charAt(0);
				int v1 = Integer.parseInt(st.nextToken());
				int v2 = Integer.parseInt(st.nextToken());
				
				if(cmd == '!') {
					int w = Integer.parseInt(st.nextToken());
					union(v1, v2, w);
				}
				else {
					int root1 = find(v1);
					int root2 = find(v2);
					
					if(root1 != root2)
						sb.append("UNKNOWN\n");
					else
						sb.append(nodeArr[v1].dist - nodeArr[v2].dist).append("\n");
				}
			}
		}
	} // main-end
	
	private static int find(int num) {
		if(nodeArr[num].parent == num)	//	자기 자신이 루트일 경우
			return num;
		
		int root = find(nodeArr[num].parent);
		nodeArr[num].dist += nodeArr[nodeArr[num].parent].dist;
		
		return nodeArr[num].parent = root;
	}
	
	private static void union(int v1, int v2, int w) {
		int root1 = find(v1);
		int root2 = find(v2);
		
		if(root1 != root2) {
			nodeArr[root1].parent = root2;
			nodeArr[root1].dist = nodeArr[v2].dist + w - nodeArr[v1].dist;
		}
	}
	
	private static class Node {
		public final int num;	//	노드 번호
		public int parent;		//	부모 번호
		public long dist;		//	num이 속한 분리집합의 루트에서 num까지의 거리
		
		public Node(int num, int parent, long dist) {
			this.num = num;
			this.parent = parent;
			this.dist = dist;
		}
		
		@Override
		public String toString() {
			return "{num : " + num + ", parent : " + parent + ", dist : " + dist + "}";
		}
	}
} // Main-class-end
```