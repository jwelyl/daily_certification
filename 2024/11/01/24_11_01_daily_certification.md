# 24_11_01_daily_certification

```
[#306 koreii] 데일리인증 20241101
1. Java Multithread, Concurrency 
- 원자적 연산
- Atomic 클래스
2. 컴퓨터공학 이론
- DBCP
- TCP 3-way Handshake
- TCP 연결 종료
- loopback
- Indexing
- B+Tree
- HTTP, SSL, 상태 코드, GET, POST
3. 코딩 테스트 대비 복습문제 풀이 
- DFS, Tree, Postorder (19641 중첩 집합 모델)
- Disjoint Set, Sorting, Sweeping (14595 동방 프로젝트 (Large))
```

# Problem Solving (Algorithm & SQL)

### BOJ 19641 중첩 집합 모델

[](https://www.acmicpc.net/problem/19641)

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
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;    //    노드 개수
    private static List<Integer>[] tree;
    private static boolean[] visited;
    private static int root;
    private static Node[] nodes;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        tree = new ArrayList[N + 1];
        nodes = new Node[N + 1];
        for(int v = 0; v <= N; v++) {
            tree[v] = new ArrayList<>();
            nodes[v] = new Node();
        }
        visited = new boolean[N + 1];
        
        for(int m = 0; m < N; m++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            
            while(true) {
                int v2 = Integer.parseInt(st.nextToken());
                
                if(v2 != -1) {
                    tree[v1].add(v2);
                    tree[v2].add(v1);
                }
                else break;
            }
        }
        
        for(int v = 0; v <= N; v++)
            Collections.sort(tree[v]);
        
        root = Integer.parseInt(br.readLine());
        
        dfs(root, 1);
        
        for(int v = 1; v <= N; v++)
            sb.append(v).append(" ").append(nodes[v]).append("\n");
        
        System.out.print(sb);
    }    //    main-end
    
    private static void dfs(int v, int left) {
        int right = left + 1;    //    v의 right 값
        visited[v] = true;
        
        int nextLeft = right;
        for(int next : tree[v]) {
            if(!visited[next]) {    //    아직 방문 안했을 경우
                dfs(next, nextLeft);
                nextLeft = nodes[next].right + 1;
                right = nextLeft;    //    v의 right는 v의 가장 마지막 자식의 right 값 + 1임
            }
        }
        
        nodes[v].left = left;
        nodes[v].right = right;
    }
    
    private static class Node {
        public int left;
        public int right;
        
        @Override
        public String toString() {
            return new StringBuilder().append(left).append(" ").append(right).toString();
        }
    }
}    //    Main-class-end
```

### BOJ 14595 동방 프로젝트 (Large)

[](https://www.acmicpc.net/problem/14595)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.List;
import java.util.ArrayList;
import java.util.Collections;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    방 개수
    private static int M;	 //	   벽 부수는 횟수

    private static int[] parents;	//	parents[v] : v의 부모, 없으면 자기 자신
    
    //	벽 부수기 순서는 상관이 없음, from이 가장 작은 벽 부수기부터 시행, from이 같을 경우 to가 가장 작은 벽 부수기부터 시행
    private static final List<Break> breaks = new ArrayList<>();
    
    private static int rooms = 0;	//	남은 방 개수
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        M = Integer.parseInt(br.readLine());
        
        rooms = N;
        parents = new int[N + 1];
        for(int v = 1; v <= N; v++)
        	parents[v] = v;
        
        for(int m = 0; m < M; m++) {
        	st = new StringTokenizer(br.readLine());
        	int from = Integer.parseInt(st.nextToken());
        	int to = Integer.parseInt(st.nextToken());
        
        	breaks.add(new Break(from, to));	//	from 방부터 to 방 사이의 모든 벽을 허뭄
        }
        
        Collections.sort(breaks);
        
        int lastRoom = 0;	//	벽을 부수는 과정에서 나타난 가장 오른쪽에 있는 방
        
        for(Break b : breaks) {
        	if(b.to <= lastRoom)	//	이미 부순 구간을 또 부술 경우 skip 가능
        		continue;
        	
        	if(b.from < lastRoom)	//	from이 lastRoom보다 작을 경우
        		b.from = lastRoom;	//	from, lastRoom 사이의 벽은 모두 부서졌으므로 lastRoom부터 부수면 된다.
        	
        	for(int v = b.from + 1; v <= b.to; v++)
        		union(b.from, v);
        	
        	lastRoom = b.to;
        }
        
        System.out.println(rooms);
    }    //    main-end
    
    private static int find(int v) {
    	if(v == parents[v])
    		return v;
    	
    	return parents[v] = find(parents[v]);
    }
    
    //	인접한 두 방 v1, v2가 합쳐짐
    private static void union(int v1, int v2) {
    	int v1Ds = find(v1);
    	int v2Ds = find(v2);
    	
    	if(v1Ds != v2Ds) {	//	두 방이 같은 집합에 속하지 않을 경우, 두 방 합쳐서 같은 집합으로 만듬
    		parents[v2Ds] = v1Ds;
    		rooms--;
    	}
    }
    
    private static class Break implements Comparable<Break> {
        public int from;
        public int to;		//	from방부터 to방 사이의 벽들을 부숨
        
        public Break(int from, int to) {
        	this.from = from;
        	this.to = to;
        }
        
        @Override
        public int compareTo(Break other) {
        	int res = Integer.compare(from, other.from);	//	from이 작은 순으로 정렬
        	
        	if(res == 0)	//	from이 같을 경우
        		res = Integer.compare(to, other.to);	//	to가 작은 순으로 정렬
        	
        	return res;
        }
    }
}    //    Main-class-end
```