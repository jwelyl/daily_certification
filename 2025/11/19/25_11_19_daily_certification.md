# 25_11_19_daily_certification

# To Do List

```
[#323 koreii] 데일리인증 20251119
1. 알고리즘 문제 풀이
- BOJ 12767 Ceiling Function (BST, Preorder, HashSet, HashMap)
```

```
[#142] 데일리인증 20251119 (323 / 365)
1. 알고리즘 문제 풀이
- BOJ 12767 Ceiling Function (BST, Preorder, HashSet, HashMap)
```

# Problem Solving (Algorithm & SQL)

### BOJ 12767 Ceiling Function

[12767번: Ceiling Function](http://boj.ma/12767/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Arrays;
import java.util.Set;
import java.util.Map;
import java.util.HashSet;
import java.util.HashMap;

public class Main {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());
        int n = Integer.parseInt(st.nextToken());    //    bst 개수
        int k = Integer.parseInt(st.nextToken());    //    bst에 속한 노드 수
        Set<String> set = new HashSet<>();
        
        for(int i = 0; i < n; i++) {
            BST bst = new BST();
            Map<Integer, Integer> map = new HashMap<>();
            int[] orig = new int[k];
            int[] sorted = new int[k];
            st = new StringTokenizer(br.readLine());
            for(int j = 0; j < k; j++) {
                orig[j] = Integer.parseInt(st.nextToken());
                sorted[j] = orig[j];
            }
            Arrays.sort(sorted);
            
            for(int j = 0; j < k; j++)
                map.put(sorted[j], j);
            
            for(int j = 0; j < k; j++) {
                int number = orig[j];
                int value = map.get(number);
                bst.insert(value);
            }
        
            set.add(bst.traversal());
        }
        
        System.out.println(set.size());
    }    //    main-end
    
    private static class Node {
        public int number;
        public Node left;
        public Node right;
        
        public Node(int number) {
            this.number = number;
            this.left = null;
            this.right = null;
        }
    }
    
    private static class BST {
        public Node root;
        
        public BST() {
            this.root = null;
        }
        
        public void insert(int number) {
            Node node = new Node(number);
            
            if(this.root == null) {
                this.root = node;
                return;
            }
            
            Node parent = this.root;
            
            while(true) {
                if(parent.number > node.number) {    //    왼쪽에 삽입해야 할 경우
                    if(parent.left == null) {
                        parent.left = node;
                        break;
                    }
                    
                    parent = parent.left;
                }
                else {    //    오른쪽에 삽입해야 할 경우
                    if(parent.right == null) {
                        parent.right = node;
                        break;
                    }
                    
                    parent = parent.right;
                }
            }
        }
        
        public String traversal() {
            StringBuilder sb = new StringBuilder();
            preorder(this.root, sb);
            
            return sb.toString();
        }
        
        private void preorder(Node node, StringBuilder sb) {
            if(node == null)
                return;
            
            sb.append(node.number).append(" ");
            preorder(node.left, sb);
            preorder(node.right, sb);
            
        }
    }
}    //    Main-class-end
```