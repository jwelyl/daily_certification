# 24_07_31_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2983 개구리 공주

[](https://www.acmicpc.net/problem/2983)

![Untitled](24_07_31_daily_certification%204a8a8e1f209b43189c054fc80df87c3b/Untitled.jpeg)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = Integer.MIN_VALUE;	//	HEAD, TAIL을 위한 DUMMY NODE에 들어갈 값
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;	//	점 개수
	
	private static char[] directions;	//	점프 방향
	
	private static final List<Point> pointList = new ArrayList<>();	//	점 리스트
	
	private static final Map<Integer, DLL> adMap = new HashMap<>();	//	Key : (x - y) 값, Value : AD 방향으로 점 연결한 DLL
	private static final Map<Integer, DLL> bcMap = new HashMap<>();	//	Key : (x + y) 값, Value : BC 방향으로 점 연결한 DLL
	
	private static int curX = NONE;		//	현재 점의 x 좌표
	private static int curY = NONE;		//	현재 점의 y 좌표
	
	private static Node adCurNode = null;	//	현재 점의 AD DDL에서의 노드 
	private static Node bcCurNode = null;	//	현재 점의 BC DDL에서의 노드
	private static Node adNextNode = null;	//	현재 점의 AD DDL에서의 다음 노드 
	private static Node bcNextNode = null;	//	현재 점의 BC DDL에서의 다음 노드
	
	private static DLL adDLL = null;		//	x - y key 값으로 얻어온 DLL
	private static DLL bcDLL = null;		//	x + y key 값으로 얻어온 DLL
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		
		directions = br.readLine().toCharArray();
		
		st = new StringTokenizer(br.readLine());
		
		curX = Integer.parseInt(st.nextToken());
		curY = Integer.parseInt(st.nextToken());
		pointList.add(new Point(curX, curY));		//	시작점 설정
		
		for(int p = 1; p < N; p++) {
			st = new StringTokenizer(br.readLine());
			
			int x = Integer.parseInt(st.nextToken());
			int y = Integer.parseInt(st.nextToken());
			pointList.add(new Point(x, y));
		}
		
		makeDLL();
		
		for(char dir : directions) {
			int adKey = adCurNode.x - adCurNode.y;
			int bcKey = bcCurNode.x + bcCurNode.y;
			
			adDLL = adMap.get(adKey);	//	현재 adNode가 위치한 AD DLL 찾기
			bcDLL = bcMap.get(bcKey);	//	현재 bcNode가 위치한 BC DLL 찾기
			
			if(dir == 'A' || dir == 'D') {	//	AD DLL에서 움직여야 할 경우
				if(dir == 'A') {	//	x 축 방향으로 오른쪽으로 이동해야 할 경우, AD DLL에서 next로 이동
					adNextNode = adCurNode.next;

					if(!adDLL.isDummyNode(adNextNode)) {	//	점프가 가능할 경우
						adDLL.delete(adCurNode);		//	AD DLL에서 현재 adNode 제거
						bcDLL.delete(adCurNode.twin);	//	BC DLL에서 현재 adNode에 대응되는 bcNode 제거
						
						adCurNode = adNextNode;			//	AD DLL에서 다음 노드로 이동
						bcCurNode = adCurNode.twin;		//	BC DLL에서 다음 노드로 이동
					}
				}
				else {				//	x 축 방향으로 왼쪽으로 이동해야 할 경우, AD DLL에서 prev로 이동
					adNextNode = adCurNode.prev;
					
					if(!adDLL.isDummyNode(adNextNode)) {	//	점프가 가능할 경우
						adDLL.delete(adCurNode);		//	AD DLL에서 현재 adNode 제거
						bcDLL.delete(adCurNode.twin);	//	BC DLL에서 현재 adNode에 대응되는 bcNode 제거
						
						adCurNode = adNextNode;			//	AD DLL에서 다음 노드로 이동
						bcCurNode = adCurNode.twin;		//	BC DLL에서 다음 노드로 이동
					}
				}
			}
			else {	//	BC DLL에서 움직여야 할 경우
				if(dir == 'B') {	//	x 축 방향으로 오른쪽으로 이동해야 할 경우, BC DLL에서 next로 이동
					bcNextNode = bcCurNode.next;

					if(!bcDLL.isDummyNode(bcNextNode)) {	//	점프가 가능할 경우
						bcDLL.delete(bcCurNode);		//	BC DLL에서 현재 bcNode 제거
						adDLL.delete(bcCurNode.twin);	//	AD DLL에서 현재 bcNode에 대응되는 adNode 제거
						
						bcCurNode = bcNextNode;			//	BC DLL에서 다음 노드로 이동
						adCurNode = bcCurNode.twin;		//	AD DLL에서 다음 노드로 이동
					}
				}
				else {				//	x 축 방향으로 왼쪽으로 이동해야 할 경우, BC DLL에서 prev로 이동
					bcNextNode = bcCurNode.prev;
					
					if(!bcDLL.isDummyNode(bcNextNode)) {	//	점프가 가능할 경우
						bcDLL.delete(bcCurNode);		//	BC DLL에서 현재 bcNode 제거
						adDLL.delete(bcCurNode.twin);	//	AD DLL에서 현재 bcNode에 대응되는 adNode 제거
						
						bcCurNode = bcNextNode;			//	BC DLL에서 다음 노드로 이동
						adCurNode = bcCurNode.twin;		//	AD DLL에서 다음 노드로 이동
					}
				}
			}
		}
		
		curX = adCurNode.x;
		curY = adCurNode.y;
		
		System.out.println(curX + " " + curY);
	}	//	main-end
	
	private static void makeDLL() {
		Collections.sort(pointList);	//	점들을 (x + y) 크기 순으로, (x + y)가 같다면 x 크기 순으로 정렬
		
		for(Point point : pointList) {
			int x = point.x;
			int y = point.y;
			Node adNode = new Node(x, y);	//	AD DLL에 넣을 노드
			Node bcNode = new Node(x, y);	//	BC DLL에 넣을 노드
			
			adNode.twin = bcNode;	//	AD DLL의 adNode에 대응되는 BC DLL의 노드 
			bcNode.twin = adNode;	//	BC DLL의 bcNode에 대응되는 AD DLL의 노드
			
			int adKey = x - y;
			int bcKey = x + y;
			
			if(adMap.containsKey(adKey))	//	adKey에 해당하는 AD DLL이 이미 존재할 경우
				adDLL = adMap.get(adKey);	//	adKey에 해당하는 AD DLL 가져오기
			else {	//	adKey에 해당하는 AD DLL이 존재하지 않을 경우
				adDLL = new DLL();			//	AD DLL 새로 생성
				adMap.put(adKey, adDLL);	//	adKey에 새로 생성한 AD DLL 할당 
			}
			
			adDLL.insertBack(adNode);	//	AD DLL의 가장 뒤에 adNode 삽입
			
			if(bcMap.containsKey(bcKey))	//	bcKey에 해당하는 BC DLL이 이미 존재할 경우
				bcDLL = bcMap.get(bcKey);	//	bcKey에 해당하는 BC DLL 가져오기
			else {	//	bcKey에 해당하는 BC DLL이 존재하지 않을 경우
				bcDLL = new DLL();			//	BC DLL 새로 생성
				bcMap.put(bcKey, bcDLL);	//	bcKey에 새로 생성한 BC DLL 할당 
			}
			
			bcDLL.insertBack(bcNode);	//	BC DLL의 가장 뒤에 bcNode 삽입
			
			if(x == curX && y == curY) {	//	현재 점(시작 점)일 경우
				adCurNode = adNode;			//	AD DLL에서의 현재 노드(시작 노드)로 설정
				bcCurNode = bcNode;			//	BC DLL에서의 현재 노드(시작 노드)로 설정
			}
		}
	}
	
	private static class Point implements Comparable<Point> {
		public int x;
		public int y;
		
		public Point(int x, int y) {
			this.x = x;
			this.y = y;
		}
		
		@Override
		public int compareTo(Point other) {
			int ret = Integer.compare(this.x + this.y, other.x + other.y);	//	(x + y) 값이 작은 순으로 정렬
			
			if(ret == 0)
				ret = Integer.compare(this.x, other.x);		//	x 값이 작은 순으로 정렬
			
			return ret;
		}
	}
	
	private static class Node {
		public int x;
		public int y;
		
		private Node prev;	//	이전 노드
		private Node next;	//	다음 노드
		private Node twin;	//	adList일 경우, bcList에 존재하는 노드, bcList일 경우, adList에 존재하는 노드
		
		public Node() {	//	head, tail 더미 노드를 위한 생성자
			this(NONE, NONE);
		}
		
		public Node(int x, int y) {
			this.x = x;
			this.y = y;
			this.prev = null;
			this.next = null;
			this.twin = null;
		}
	}
	
	private static class DLL {
		public Node head;	//	head 더미 노드
		public Node tail;	//	tail 더미 노드
		
		public DLL() {	//	빈 DLL 생성
			this.head = new Node();		//	head 생성
			this.tail = new Node();		//	tail 생성
			
			this.head.next = this.tail;	//	head의 다음 노드는 tail
			this.tail.prev = this.head;	//	tail의 이전 노드는 head
		}
		
		public void insertBack(Node node) {	//	DLL의 가장 뒤에 노드 삽입
			Node prev = this.tail.prev;	//	현재 가장 뒤의 노드, node의 앞 노드가 될 노드
			
			node.prev = prev;
			prev.next = node;		//	prev와 node 연결
			
			node.next = this.tail;
			this.tail.prev = node;	//	node와 tail 연결	
		}
	
		public void delete(Node node) {	//	DLL에서 node 제거
			Node prev = node.prev;	//	node의 이전 노드
			Node next = node.next;	//	node의 다음 노드
			
			prev.next = next;
			next.prev = prev;	//	node를 건너뛰고 prev와 next를 연결
		}
		
		public boolean isDummyNode(Node node) {	//	주어진 노드가 head 또는 tail의 DUMMY NODE일 경우
			return node == this.head || node == this.tail;
		}
	}
}	//	Main-class-end

```