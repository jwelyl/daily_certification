# 25_10_23_daily_certification

# Problem Solving (Algorithm & SQL)

### 프로그래머스 표 편집

[](https://school.programmers.co.kr/learn/courses/30/lessons/81303?language=java)

```java
import java.util.StringTokenizer;
import java.util.Deque;
import java.util.ArrayDeque;

class Solution {
    private StringTokenizer st;
    private final DLL dll = new DLL();
    private final Deque<Integer> stack = new ArrayDeque<>();
    private Node[] nodePointers;
    
    public String solution(int n, int k, String[] cmd) {
        StringBuilder sb = new StringBuilder();
        nodePointers = new Node[n];
        
        for(int i = 0; i < n; i++) {
            nodePointers[i] = new Node(i);
            dll.offerLast(nodePointers[i]);
        }
        dll.movePointer(k + 1, true);

        for(String query : cmd) {
            
            
            st = new StringTokenizer(query);
            String option = st.nextToken();
            int cnt = 0;
            
            if(option.equals("U")) {
                cnt = Integer.parseInt(st.nextToken());
                dll.movePointer(cnt, false);
            }
            else if(option.equals("D")) {
                cnt = Integer.parseInt(st.nextToken());
                dll.movePointer(cnt, true);
            }
            else if(option.equals("C")) {
                int num = dll.delete();
                stack.offerLast(num);
            }
            else {
                int num = stack.pollLast();
                dll.recover(nodePointers[num]);
            }
        }
        
        for(int i = 0; i < n; i++)
            sb.append(nodePointers[i].deleted ? "X" : "O");
        
        return sb.toString();
    }
    
    private static class Node {
        public final int num;
        public Node prev = null;
        public Node next = null;
        public boolean deleted = false;
        
        public Node(int num) {
            this.num = num;
        }
    }
    
    private static class DLL {
        public Node head;
        public Node tail;
        public Node pointer;
        
        public DLL() {
            this.head = new Node(-1);
            this.tail = new Node(-1);
            this.head.next = this.tail;
            this.tail.prev = this.head;
            this.pointer = this.head;
        }
        
        public void offerLast(Node node) {
            node.prev = this.tail.prev;
            node.next = this.tail;
            this.tail.prev.next = node;
            this.tail.prev = node;
        }
    
        public void movePointer(int cnt, boolean dir) {
            for(int i = 0; i < cnt; i++)
                this.pointer = dir ? this.pointer.next : this.pointer.prev;
        }
        
        public int delete() {
            Node delNode = this.pointer;
            Node delNextNode = delNode.next == this.tail ? delNode.prev : delNode.next;
            
            delNode.prev.next = delNode.next;
            delNode.next.prev = delNode.prev;
            
            delNode.deleted = true;
            this.pointer = delNextNode;
            return delNode.num;
        }
        
        public void recover(Node node) {
            node.next.prev = node;
            node.prev.next = node;
            node.deleted = false;
        }
    }
}
```