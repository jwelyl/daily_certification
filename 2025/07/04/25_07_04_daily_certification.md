# 25_07_04_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 32028 이진 검색 트리 복원하기

[32028번: 이진 검색 트리 복원하기](http://boj.ma/32028/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.TreeMap;
import java.util.TreeSet;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
    private static int N;
    
    private static Node[] nodeArr;
    private static final TreeMap<Integer, TreeSet<Node>> map = new TreeMap<>();
    
    private static int prevH = 0;
    
    public static void main(String[] args) throws IOException {
    	N = Integer.parseInt(br.readLine());
    	
    	nodeArr = new Node[N + 1];
    	for(int num = 1; num <= N; num++) {
    		st = new StringTokenizer(br.readLine());
    		int value = Integer.parseInt(st.nextToken());
    		int height = Integer.parseInt(st.nextToken());
    		
    		nodeArr[num] = new Node(num, value);
    		
    		TreeSet<Node> nodeSet = map.getOrDefault(height, new TreeSet<>());
    		nodeSet.add(nodeArr[num]);
    		map.put(height, nodeSet);
    	}
    	
    	for(int height : map.keySet()) {	//	높이 height인 노드들을 트리에 복구할 차례
    		if(prevH != height - 1) {
    			System.out.println(-1);
    			return;
    		}
    		
    		TreeSet<Node> curNodeSet = map.get(height);	//	높이가 height인 노드 집합
    		if(height == 1) {
    			if(curNodeSet.size() > 1) {	//	루트 노드가 2개 이상일 경우
    				System.out.println(-1);
        			return;
    			}

    			Node root = curNodeSet.first();
    			root.min = Integer.MIN_VALUE;
    			root.max = Integer.MAX_VALUE;
    			
    			prevH = height;
    			continue;	//	루트 노드는 부모가 없음
    		}
    		
    		TreeSet<Node> prevNodeSet = map.get(prevH);	//	높이가 height인 노드 집합의 부모 집합
    		for(Node curNode : curNodeSet) {
    			Node smaller = prevNodeSet.floor(curNode);	//	curNode의 부모 노드 후보 중 curNode의 value보다 value가 작으면서 최대 value를 가지는 노드
    			Node bigger = prevNodeSet.higher(curNode);	//	curNode의 부모 노드 후보 중 curNode의 value보다 value가 크면서 최소 value를 가지는 노드
    		
    			if (smaller != null && smaller.rightChild == null && curNode.value > smaller.value && curNode.value < smaller.max) {
    			    smaller.rightChild = curNode;
    			    curNode.min = smaller.value;
    			    curNode.max = smaller.max;
    			} else if (bigger != null && bigger.leftChild == null && curNode.value < bigger.value && curNode.value > bigger.min) {
    			    bigger.leftChild = curNode;
    			    curNode.min = bigger.min;
    			    curNode.max = bigger.value;
    			} else {	// 부모 노드를 찾을 수 없는 경우
    			    System.out.println(-1);
    			    return;
    			}
    		}
    		
    		prevH = height;
    	}
    	
    	for(int num = 1; num <= N; num++)
    		sb.append(nodeArr[num]).append("\n");
    	
    	System.out.print(sb);
    }	//	main-end

    private static class Node implements Comparable<Node> {
    	public int num;	//	노드 번호
    	public int value;	//	노드의 값
    	public Node leftChild;
    	public Node rightChild;
    	
    	// 추가된 필드: 유효한 범위
    	public int min = Integer.MIN_VALUE;
    	public int max = Integer.MAX_VALUE;
    	
    	public Node(int num, int value) {
    		this.num = num;
    		this.value = value;
    		this.leftChild = null;
    		this.rightChild = null;
    	}
    	
    	@Override
    	public int compareTo(Node other) {
    		return Integer.compare(this.value, other.value);
    	}
    	
    	@Override
    	public String toString() {
    		return new StringBuilder()
    				.append(this.leftChild == null ? -1 : this.leftChild.num)
    				.append(" ")
    				.append(this.rightChild == null ? -1 : this.rightChild.num)
    				.toString();
    	}
    }
}	//	Main-class-end
```