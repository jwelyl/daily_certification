# 25_06_05_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 14867 물통

[14867번: 물통](http://boj.ma/14867/t)

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
	
	private static int CA;
	private static int CB;	//	A, B 물통의 용량
	private static int TA;
	private static int TB;	//	A, B 물통에 남기길 원하는 물의 양
	
	private static final Set<Bottle> visited = new HashSet<>();
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		CA = Integer.parseInt(st.nextToken());
		CB = Integer.parseInt(st.nextToken());
		TA = Integer.parseInt(st.nextToken());
		TB = Integer.parseInt(st.nextToken());
		
		if(TA == 0 && TB == 0) {
			System.out.println(0);
			return;
		}
		
		System.out.println(bfs());
	}	//	main-end
	
	private static int bfs() {
		Queue<int[]> queue = new LinkedList<>();
		
		visited.add(new Bottle(0, 0));
		queue.offer(new int[] {0, 0, 0});
		
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int ca = cur[0];
			int cb = cur[1];
			int ccnt = cur[2];
			
			if(operation1(ca, cb, ccnt, queue))
				return ccnt + 1;
			if(operation2(ca, cb, ccnt, queue))
				return ccnt + 1;
			if(operation3(ca, cb, ccnt, queue))
				return ccnt + 1;
		}
		
		return -1;
	}
	
	//	A, B 중 한 물통을 골라서 물을 가득 채움
	private static boolean operation1(int ca, int cb, int ccnt, Queue<int[]> queue) {
		//	1. A 물통을 비움
		int na = 0;
		int nb = cb;
		
		if(na == TA && nb == TB)
			return true;
		
		Bottle nBottle1 = new Bottle(na, nb);
		if(!visited.contains(nBottle1)){
			visited.add(nBottle1);
			queue.offer(new int[] {na, nb, ccnt + 1});
		}
		
		//	2. B 물통을 비움
		na = ca;
		nb = 0;
		
		if(na == TA && nb == TB)
			return true;
		
		Bottle nBottle2 = new Bottle(na, nb);
		if(!visited.contains(nBottle2)) {
			visited.add(nBottle2);
			queue.offer(new int[] {na, nb, ccnt + 1});
		}
		
		return false;
	}
	
	//	A, B 중 한 물통을 골라서 물을 비움
	private static boolean operation2(int ca, int cb, int ccnt, Queue<int[]> queue) {
		//	1. A 물통을 가득 채움
		int na = CA;
		int nb = cb;
		
		if(na == TA && nb == TB)
			return true;
		
		Bottle nBottle1 = new Bottle(na, nb);
		if(!visited.contains(nBottle1)){
			visited.add(nBottle1);
			queue.offer(new int[] {na, nb, ccnt + 1});
		}
		
		//	2. B 물통을 가득 채움
		na = ca;
		nb = CB;
		
		if(na == TA && nb == TB)
			return true;
		
		Bottle nBottle2 = new Bottle(na, nb);
		if(!visited.contains(nBottle2)) {
			visited.add(nBottle2);
			queue.offer(new int[] {na, nb, ccnt + 1});
		}
		
		return false;
	}
	
	//	A, B 중 한 물통을 선택해 다른 물통에 물을 부음
	private static boolean operation3(int ca, int cb, int ccnt, Queue<int[]> queue) {
		//	1. A 물통의 물을 B 물통에 부음
		int na = ca >= (CB - cb) ? ca - (CB - cb) : 0;
		int nb = ca >= (CB - cb) ? CB : cb + ca;
		
		if(na == TA && nb == TB)
			return true;
		
		Bottle nBottle1 = new Bottle(na, nb);
		if(!visited.contains(nBottle1)){
			visited.add(nBottle1);
			queue.offer(new int[] {na, nb, ccnt + 1});
		}
		
		//	2. B 물통의 물을 A 물통에 부음
		na = cb >= (CA - ca) ? CA : ca + cb;
		nb = cb >= (CA - ca) ? cb - (CA - ca) : 0;
		
		if(na == TA && nb == TB)
			return true;
		
		Bottle nBottle2 = new Bottle(na, nb);
		if(!visited.contains(nBottle2)) {
			visited.add(nBottle2);
			queue.offer(new int[] {na, nb, ccnt + 1});
		}
		
		return false;
	}
	
	private static class Bottle {
		public int a;
		public int b;
		
		public Bottle(int a, int b) {
			this.a = a;
			this.b = b;
		}
		
		@Override
		public boolean equals(Object obj) {
			if(this == obj)
				return true;
			
			if(obj == null || this.getClass() != obj.getClass())
				return false;
			
			Bottle bottle = (Bottle)obj;
			return this.a == bottle.a && this.b == bottle.b;
		}
		
		@Override
		public int hashCode() {
			return a * 100_001 + b;
		}
	}
}	//	Main-class-end
```

### BOJ 1863 스카이라인 쉬운거

[1863번: 스카이라인 쉬운거](http://boj.ma/1863/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Stack;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	
	private static final Stack<Integer> stack = new Stack<>();
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		for(int i = 0; i < N; i++) {
			st = new StringTokenizer(br.readLine());
			Integer.parseInt(st.nextToken());
			int h = Integer.parseInt(st.nextToken());
			
			if(stack.isEmpty()) {
				if(h != 0)
					stack.push(h);
			}
			else {
				if(h > stack.peek())
					stack.push(h);
				else  {
					while(!stack.isEmpty() && stack.peek() > h) {
						answer++;
						stack.pop();
					}
					
					if(h != 0) {
						if(stack.isEmpty() || stack.peek() < h)
							stack.push(h);
					}
				}
			}
		}
		
		answer += stack.size();
		System.out.println(answer);
	}	//	main-end
}	//	Main-class-end
```