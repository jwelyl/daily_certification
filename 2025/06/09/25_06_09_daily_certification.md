# 25_06_09_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 17469 트리의 색깔과 쿼리

[17469번: 트리의 색깔과 쿼리](http://boj.ma/17469/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashSet;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int N;
	private static int Q;
	
	private static int[] parents;
	private static int[] dsParents;
	
	//	colorSets[v] : v가 우두머리인 분리집합에 포함된 정점 색 집합
	private static Set<Integer>[] colorSets;
	
	//	N - 1개의 1번 쿼리 + Q개의 2번 쿼리
	private static int[][] queries;
	
	private static int idx;
	//	answers[q] : q번째 2번 쿼리에 대한 정답
	private static int[] answers;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		Q = Integer.parseInt(st.nextToken());
		
		parents = new int[N + 1];
		dsParents = new int[N + 1];
		colorSets = new HashSet[N + 1];
		
		queries = new int[N + Q - 1][2];
		
		idx = Q - 1;
		answers = new int[Q];
		
		for(int v = 1; v <= N; v++) {
			dsParents[v] = v;
			colorSets[v] = new HashSet<>();
		}
		
		for(int v = 2; v <= N; v++)
			parents[v] = Integer.parseInt(br.readLine());
		
		for(int v = 1; v <= N; v++)
			colorSets[v].add(Integer.parseInt(br.readLine()));
		
		for(int q = 0; q < N + Q - 1; q++) {
			st = new StringTokenizer(br.readLine());
			queries[q][0] = Integer.parseInt(st.nextToken());
			queries[q][1] = Integer.parseInt(st.nextToken());
		}
		
		for(int q = N + Q - 2; q >= 0; q--) {
			int[] query = queries[q];
			int opt = query[0];
			int v = query[1];
		
			if(opt == 1) {
				int p = parents[v];
				union(v, p);
			}
			else
				answers[idx--] = colorSets[find(v)].size();
		}
		
		for(int q = 0; q < Q; q++)
			sb.append(answers[q]).append("\n");
		
		System.out.print(sb);
	} //	main-end
	
	private static int find(int v) {
		return v == dsParents[v] ? v : (dsParents[v] = find(dsParents[v]));
	}
	
	private static void union(int v1, int v2) {
		v1 = find(v1);
		v2 = find(v2);
		
		if(v1 != v2) {
			if(colorSets[v1].size() < colorSets[v2].size()) {
				int temp = v1;
				v1 = v2;
				v2 = temp;
			}
			dsParents[v2] = v1;
			colorSets[v1].addAll(colorSets[v2]);
			colorSets[v2].clear();
		}
	}
} //	Main-class-end
```