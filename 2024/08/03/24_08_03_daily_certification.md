# 24_08_03_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 31857 사탕 공장

[](https://www.acmicpc.net/problem/31857)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static final DLL[] dlls = new DLL[2];

	private static int N;
	private static int R;
	private static int Q;
	private static int x;

	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		R = Integer.parseInt(st.nextToken());
		Q = Integer.parseInt(st.nextToken());

		dlls[0] = new DLL();
		dlls[1] = new DLL();
		dlls[0].init();
		dlls[1].init();
		
		for (int q = 0; q < Q; q++) {
			st = new StringTokenizer(br.readLine());
			char cmd = st.nextToken().charAt(0);

			switch (cmd) {
			case 'S':
				dlls[0].swap(dlls[1]);
				break;
			case 'L':
				x = Integer.parseInt(st.nextToken()) - 1;
				dlls[x].left();
				break;
			case 'R':
				x = Integer.parseInt(st.nextToken()) - 1;
				dlls[x].right();
				break;
			case 'I':
				dlls[0].increase();
				dlls[1].increase();
				break;
			case 'D':
				dlls[0].decrease();
				dlls[1].decrease();
				break;
			}
		}

		dlls[0].print();
		dlls[1].print();

		System.out.print(sb);
	} // main-end

	private static class Node {
		public char elem;
		public Node prev;
		public Node next;

		public Node(char elem) {
			this.elem = elem;
			this.prev = null;
			this.next = null;
		}
	}

	private static class DLL {
		public final Node head; // DLL head
		public final Node tail; // DLL tail
		public Node first; // 첫 번째 노드 가리킴
		public Node last; // R번째 노드 가리킴

		public DLL() {
			this.head = new Node(' ');
			this.tail = new Node(' ');

			this.head.next = this.tail;
			this.tail.prev = this.head;
		}

		public void init() throws IOException {
			char[] elems = br.readLine().toCharArray(); // 사탕들

			for (int idx = 0; idx < N; idx++) {
				Node node = new Node(elems[idx]);
				this.insertBack(node); // 사탕을 가장 끝에 놓기

				if (idx == R - 1) // R번째 사탕일 경우
					this.last = node;
			}

			this.first = this.head.next;
		}

		public void swap(DLL other) {
			Node next1 = this.last.next; // 현재 DLL의 last의 다음 노드
			Node next2 = other.last.next; // other DLL의 last의 다음 노드

			this.head.next = other.first;
			other.first.prev = this.head; // 현재 DLL의 head와 other의 첫번째 노드 연결
			other.last.next = next1;
			next1.prev = other.last; // next1이 null인지 확인

			other.head.next = this.first;
			this.first.prev = other.head; // other의 head와 현재 DLL의 첫번째 노드 연결
			this.last.next = next2;
			next2.prev = this.last; // next2가 null인지 확인

			this.first = this.head.next;
			other.first = other.head.next; // first 재조정
			this.last = next1.prev;
			other.last = next2.prev; // last 재조정
		}

		public void left() { // 첫 번째 노드를 꺼내서 마지막에 넣기
			if (this.head.next == this.tail.prev) // 노드가 하나일 경우
				return;

			Node nFirst = this.first.next; // 새로운 첫 번째 노드

			this.last = this.last.next; // last 자리 유지를 위해 오른쪽으로 한 칸 이동

			this.head.next = nFirst;
			nFirst.prev = this.head; // head와 새로운 첫 번째 노드 연결

			this.insertBack(this.first); // 이전 첫 번째 노드를 가장 뒤에 넣음

			if (this.last == this.tail) //	원래 last가 가장 마지막 노드였을 경우
				this.last = this.tail.prev;	//	가장 마지막 노드로 재배치

			this.first = this.head.next; // first 노드 재배치
		}

		public void right() {
			if (this.head.next == this.tail.prev) // 노드가 하나일 경우
				return;

			Node end = this.tail.prev; // 이전 마지막 노드
			Node nEnd = end.prev; // 새로운 마지막 노드
			
			this.last = this.last.prev;	//	last 자리 유지를 위해 왼쪽으로 한 칸 이동

			nEnd.next = this.tail;
			this.tail.prev = nEnd; // 새로운 마지막 노드와 tail 연결

			this.insertFront(end); // 이전 마지막 노드를 가장 앞에 넣음
			
			if(this.last == this.head)	//	원래 last가 가장 첫번째 노드였을 경우
				this.last = this.head.next;	//	가장 첫번째 노드로 재배치

			this.first = this.head.next;
		}

		public void increase() {
			if (this.last.next != null) {
				this.last = this.last.next; // last 1칸 오른쪽 이동
			}
		}

		public void decrease() {
			if (this.last.prev != null) {
				this.last = this.last.prev; // last 1칸 왼쪽 이동
			}
		}

		private void insertFront(Node node) { // DLL 가장 앞에 node 삽입
			Node next = this.head.next; // next 앞에 node를 삽입

			next.prev = node;
			node.next = next; // node와 next 연결
			this.head.next = node;
			node.prev = this.head; // head와 node 연결
		}

		private void insertBack(Node node) { // DLL 가장 뒤에 node 삽입
			Node prev = this.tail.prev; // prev 뒤에 node를 삽입

			prev.next = node;
			node.prev = prev; // prev와 node 연결
			this.tail.prev = node;
			node.next = this.tail; // node와 tail 연결
		}

		public void print() {
			Node cur = this.first;

			while (cur != this.tail) {
				sb.append(cur.elem);
				cur = cur.next;
			}
			sb.append("\n");
		}
	}
} // Main-class-end
```