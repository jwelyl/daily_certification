# 24_06_15_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 28707 배열 정렬

[](https://www.acmicpc.net/problem/28707)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer tokens;
	
	private static final Map<String, Integer> valueMap = new HashMap<>();		//	Key : 배열 값, Value : 정점 번호
	private static final Map<Integer, int[]> vertexMap = new HashMap<>();	//	Key : 정점 번호, Value : 배열 상태
	
	private static int N;	//	배열 원소 개수
	private static int M;	//	조작 개수
	
	private static int[] arr;
	private static int[][] operations;
	
	private static int vNum = 0;			//	정점 개수
	
	private static String startValue = "";	//	시작 정점 값
	private static int startVertex = 0;		//	시작 정점
	
	private static String minValue = "";	//	정점 최솟값
	private static int minVertex = 0;		//	그 때의 정점
	
	private static List<Node>[] graph;
	private static int[] dist;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		arr = new int[N];
		
		tokens = new StringTokenizer(br.readLine());
		for(int i = 0; i < N; i++)
			arr[i] = Integer.parseInt(tokens.nextToken());
		
		startValue = value(arr);
		
		Arrays.sort(arr);
		minValue = value(arr);
		
		M = Integer.parseInt(br.readLine());
		operations = new int[M][3];
		
		for(int i = 0; i < M; i++) {
			tokens = new StringTokenizer(br.readLine());
			operations[i][0] = Integer.parseInt(tokens.nextToken()) - 1;
			operations[i][1] = Integer.parseInt(tokens.nextToken()) - 1;
			operations[i][2] = Integer.parseInt(tokens.nextToken());
		}
		
		permutation(0, new boolean[N], new int[N]);
		
		graph = new ArrayList[vNum + 1];
		for(int i = 0; i <= vNum; i++)
			graph[i] = new ArrayList<>();
		
		dist = new int[vNum + 1];
		Arrays.fill(dist, Integer.MAX_VALUE);
		
		for(int i = 1; i <= vNum; i++) {
			int[] curArr = vertexMap.get(i);
			
			for(int j = 0; j < M; j++) {
				int left = operations[j][0];
				int right = operations[j][1];
				int cost = operations[j][2];
				
				int tmp = curArr[left];
				curArr[left] = curArr[right];
				curArr[right] = tmp;
				
				String nextValue = value(curArr);
				int nextV = valueMap.get(nextValue);
				
				graph[i].add(new Node(nextV, cost));
				graph[nextV].add(new Node(i, cost));
				
				tmp = curArr[left];
				curArr[left] = curArr[right];
				curArr[right] = tmp;
			}
		}
		
		dijkstra();
		
		System.out.println(dist[minVertex] == Integer.MAX_VALUE ? -1 : dist[minVertex]);
	}	//	main-end
	
	private static void dijkstra() {
		PriorityQueue<Node> pq = new PriorityQueue<>();
		dist[startVertex] = 0;
		pq.offer(new Node(startVertex, dist[startVertex]));
		
		while(!pq.isEmpty()) {
			Node cur = pq.poll();
			int curV = cur.vertex;
			int curC = cur.cost;
			
			if(curC > dist[curV])
				continue;
			
			for(Node next : graph[curV]) {
				int nextV = next.vertex;
				int nextC = curC + next.cost;
				
				if(dist[nextV] > nextC) {
					dist[nextV] = nextC;
					pq.offer(new Node(nextV, dist[nextV]));
				}
			}
		}
	}
	
	private static int[] copy(int[] arr) {
		int[] copied = new int[arr.length];
		for(int i = 0; i < arr.length; i++)
			copied[i] = arr[i];
		
		return copied;
	}
	
	private static void permutation(int nth, boolean[] used, int[] result) {
		if(nth == N) {
			String value = value(result);
			
			if(!valueMap.containsKey(value)) {
				vNum++;
				valueMap.put(value, vNum);
				vertexMap.put(vNum, copy(result));
				
				if(value.equals(minValue))
					minVertex = vNum;
				
				if(value.equals(startValue))
					startVertex = vNum;
			}
			
			return;
		}
		
		for(int i = 0; i < N; i++) {
			if(!used[i]) {
				used[i] = true;
				result[nth] = arr[i];
				permutation(nth + 1, used, result);
				used[i] = false;
			}
		}
	}
	
	private static String value(int[] arr) {
		StringBuilder builder = new StringBuilder("");
		for(int i = 0; i < arr.length; i++)
			builder.append(arr[i]);
		
		return builder.toString();
	}
	
	private static class Node implements Comparable<Node> {
		int vertex;
		int cost;
		
		public Node(int vertex, int cost) {
			this.vertex = vertex;
			this.cost  = cost;
		}
		
		@Override
		public int compareTo(Node other) {
			return Integer.compare(this.cost, other.cost);
		}
	}
}	//	Main-class-end
```