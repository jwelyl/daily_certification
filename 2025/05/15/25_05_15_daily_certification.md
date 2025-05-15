# 25_05_15_daily_certification

# To Do List

- [ ]  백준 1문제
- [ ]  Algoduck 채점 서버 구현
- [ ]  SQLD 문제 풀이
- [ ]  리눅스마스터 2급 2차 공부

```
[#135 koreii] 데일리인증 20250515
1. 코딩 테스트 대비 문제 풀이
- Disjoint Set (BOJ 23324 **어려운 모든 정점 쌍 최단 거리**)
```

# Problem Solving (Algorithm & SQL)

### BOJ 23324 **어려운 모든 정점 쌍 최단 거리**

[23324번: 어려운 모든 정점 쌍 최단 거리](http://boj.ma/23324/t)

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
	private static int K;

	private static int[] parents;
	private static int[] dsSizes;
	
	private static int kV1;
	private static int kV2;	//	가중치 1인 간선의 두 정점
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		parents = new int[N + 1];
		dsSizes = new int[N + 1];
		
		for(int v = 1; v <= N; v++) {
			parents[v] = v;
			dsSizes[v] = 1;
		}
		
		for(int e = 1; e <= M; e++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			
			if(e == K) {
				kV1 = v1;
				kV2 = v2;
			}
			else
				union(v1, v2);
		}
		
		kV1 = find(kV1);
		kV2 = find(kV2);
		
		if(kV1 != kV2)
			System.out.println((long)dsSizes[kV1] * dsSizes[kV2]);
		else
			System.out.println(0);
	}	//	main-end
	
	private static int find(int v) {
		return v == parents[v] ? v : (parents[v] = find(parents[v]));
	}
	
	private static void union(int v1, int v2) {
		v1 = find(v1);
		v2 = find(v2);
		
		if(v1 != v2) {
			parents[v2] = v1;
			dsSizes[v1] += dsSizes[v2];
			dsSizes[v2] = 0;
		}
	}

}	//	Main-class-end
```