# 25_08_27_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 9097 Quad Trees

[9097번: Quad Trees](http://boj.ma/9097/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.LinkedList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int T;    //    테스트케이스 개수
    private static int N;    //    쿼드트리 크기
    private static char[][] quadtree;
    private static TreeNode root;
    
    public static void main(String[] args) throws IOException {
        T = Integer.parseInt(br.readLine());
        
        for(int tc = 1; tc <= T; tc++) {
            N = Integer.parseInt(br.readLine());
            quadtree = new char[N][N];
            
            for(int y = 0; y < N; y++) {
                st = new StringTokenizer(br.readLine());
                for(int x = 0; x < N; x++)
                    quadtree[y][x] = Integer.parseInt(st.nextToken()) == 0 ? '0' : '1';
            }
            
            root = recursion(0, 0, N - 1, N - 1);
            bfs();
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static TreeNode recursion(int sy, int sx, int ey, int ex) {
        TreeNode node;
        
        if(sy == ey && sx == ex) {    //    1 * 1 쿼드트리일 경우
            node = new TreeNode(true, quadtree[sy][sx]);
            return node;
        }
        
        int my = (sy + ey) / 2;
        int mx = (sx + ex) / 2;
        
        TreeNode child1 = recursion(sy, sx, my, mx);
        TreeNode child2 = recursion(sy, mx + 1, my, ex);
        TreeNode child3 = recursion(my + 1, sx, ey, mx);
        TreeNode child4 = recursion(my + 1, mx + 1, ey, ex);
        
        if((child1.same && child2.same && child3.same && child4.same) && 
           (child1.value == child2.value && child2.value == child3.value && child3.value == child4.value))
             node = new TreeNode(true, child1.value);
        else {
            node = new TreeNode();
            node.children[0] = child1;
            node.children[1] = child2;
            node.children[2] = child3;
            node.children[3] = child4;
        }
        
        return node;
    }
    
    private static void bfs() {
        Queue<TreeNode> queue = new LinkedList<>();
        StringBuilder res = new StringBuilder();
        
        res.append(root);
        queue.offer(root);
        
        while(!queue.isEmpty()) {
            TreeNode cur = queue.poll();
            
            if(cur.children != null) {
                for(int i = 0; i < 4; i++) {
                    res.append(cur.children[i]);
                    queue.offer(cur.children[i]);
                }
            }
        }
        
        sb.append(convertBinToHex(res.toString())).append("\n");
    }
    
    private static String convertBinToHex(String bin) {
        StringBuilder hex = new StringBuilder();
        
        int r = bin.length() % 4;
        String chunk = bin.substring(0, r);
        hex.append(convertBinToHex4(chunk));
        
        while(r < bin.length()) {
            chunk = bin.substring(r, r + 4);
            hex.append(convertBinToHex4(chunk));
            r += 4;
        }
        
        return hex.toString();
    }
    
    private static char convertBinToHex4(String chunk) {
        int sum = 0;
        int mult = 1;
        
        for(int i = chunk.length() - 1; i >= 0; i--) {
            sum += (chunk.charAt(i) - '0') * mult;
            mult *= 2;
        }
        
        return 0 <= sum && sum <= 9 ? (char)('0' + sum) : (char)('A' + (sum - 10));
    }
    
    private static class TreeNode {
        public boolean same;    //    쿼드트리 내부 값이 전부 같을 경우 true, 다르면 false
        public char value;       //    쿼드트리 내부 값이 전부 같을 경우, 그 값
        public TreeNode[] children;
        
        public TreeNode(boolean same, char value) {
            this.same = same;
            this.value = value;
            this.children = null;
        }
        
        public TreeNode() {
            this.same = false;
            this.children = new TreeNode[4];
        }
        
        @Override
        public String toString() {
            StringBuilder sb = new StringBuilder();
            
            if(same)
                sb.append("0").append(value);
            else
                sb.append("1");
            
            return sb.toString();
        }
    }
}    //    Main-class-end
```