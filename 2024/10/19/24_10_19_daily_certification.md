# 24_10_19_daily_certification

# Problem Solving (Algorithm & SQL)

### **CO{)E TREE** 트리 중위 순회

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/9/problems/tree-inorder/description)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int K;
    private static int[] inorders;
    
    private static Node root = null;
    
    public static void main(String[] args) throws IOException {
    	K = Integer.parseInt(br.readLine());
    	
    	inorders = new int[(int)Math.pow(2, K) - 1];
    	
    	st = new StringTokenizer(br.readLine());
    	for(int i = 0; i < inorders.length; i++)
    		inorders[i] = Integer.parseInt(st.nextToken());
        
    	root = makeTree(0, inorders.length - 1);
    	
    	bfs();
    }    //    main-end
    
    private static Node makeTree(int start, int end) {
    	int mid = (start + end) / 2;
    	
    	Node node = new Node(inorders[mid]);
    	
    	if(start != end) {
    		node.left = makeTree(start, mid - 1);
    		node.right = makeTree(mid + 1, end);
    	}
    	
    	return node;
    }
    
    private static void bfs() {
    	Queue<Node> q = new LinkedList<>();
    	q.offer(root);
    	sb.append(root.num).append("\n");
    	
    	while(!q.isEmpty()) {
    		int size = q.size();
    		
    		for(int s = 0; s < size; s++) {
    			Node cur = q.poll();
    			
    			if(cur.left != null) {
    				sb.append(cur.left.num).append(" ");
    				q.offer(cur.left);
    			}
    			if(cur.right != null) {
    				sb.append(cur.right.num).append(" ");
    				q.offer(cur.right);
    			}
    		}
    		
    		sb.append("\n");
    	}
    	
    	System.out.print(sb);
    }
    
    private static class Node {
    	public int num;
    	public Node left;
    	public Node right;
    	
    	public Node(int num) {
    		this.num = num;
    		this.left = null;
    		this.right = null;
    	}
    }
}    //    Main-class-end
```