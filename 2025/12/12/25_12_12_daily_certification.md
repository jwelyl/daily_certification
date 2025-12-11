# 25_12_12_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 3217 malloc

[3217번: malloc](http://boj.ma/3217/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;

public class Main {
  private static final int NONE = 0;
  private static final int MAX_ADDRESS = 100_000;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    DLL dll = new DLL();
    int n = Integer.parseInt(br.readLine());

    for(int q = 0; q < n; q++) {
      String input = br.readLine();
      char type = input.charAt(4);

      if(type == '=') { //  malloc 명령어일 경우
        String name = input.substring(0, 4);
        int size = Integer.parseInt(input.substring(12, input.length() - 2));
        dll.malloc(name, size);
      }
      else if(type == '(')  { //  free 명령어일 경우
        String name = input.substring(5, input.length() - 2);
        dll.free(name);
      }
      else {  //  print 명령어일경우
        String name = input.substring(6, input.length() - 2);
        sb.append(dll.print(name)).append("\n");
      }
    }

    System.out.print(sb);
  }    //    main-end

  private static class Node {
    public int startAddress;
    public int endAddress;
    public Node prev;
    public Node next;

    public Node(int startAddress, int endAddress) {
      this.startAddress = startAddress;
      this.endAddress = endAddress;
      this.prev = null;
      this.next = null;
    }
  }

  private static class DLL {
    public Node head;
    public Node tail;
    public final int capacity;  //  메모리 최대 용량
    public int size;  //  메모리 사용량
    public final Map<String, Node> pointerMap;

    public DLL() {
      this.head = new Node(NONE, NONE);
      this.tail = new Node(MAX_ADDRESS + 1, MAX_ADDRESS + 1);
      this.head.next = tail;
      this.tail.prev = head;
      this.capacity = MAX_ADDRESS;
      this.size = 0;
      this.pointerMap = new HashMap<>();
    }

    public void malloc(String name, int size) {
      this.pointerMap.remove(name);         //  일단 name 변수가 가리키는 포인터 제거, 실패 시 그대로 제거된 상태 유지

      if(this.size + size > this.capacity)  //  파편화를 고려하지 않아도 남는 공간이 부족할 경우
        return;

      Node prev = this.head;

      while(prev != this.tail) {
        Node next = prev.next;  //  prev와 next 사이가 빈 공간임
        int startAddress = prev.endAddress + 1;
        int endAddress = startAddress + size - 1;

        if(endAddress < next.startAddress) {  //  prev와 next 사이 공간에 할당 가능할 경우 할당하기
          Node node = new Node(startAddress, endAddress);
          this.pointerMap.put(name, node);
          this.insertNode(prev, node);
          this.size += size;
          return;
        }

        prev = next;
      }
    }

    public void free(String name) {
      Node pointer = pointerMap.get(name);

      if(pointer != null) {
        this.size -= (pointer.endAddress - pointer.startAddress + 1);
        deleteNode(pointer);
        this.pointerMap.remove(name);
      }
    }

    public int print(String name) {
      Node pointer = this.pointerMap.get(name);
      return pointer == null ? NONE : pointer.startAddress;
    }

    //  prev 노드 뒤에 node 삽입
    private void insertNode(Node prev, Node node) {
      node.prev = prev;
      node.next = prev.next;
      prev.next.prev = node;
      prev.next = node;
    }

    //  node를 연결리스트에서 제거하기
    private void deleteNode(Node node) {
      node.prev.next = node.next;
      node.next.prev = node.prev;
    }
  }
}    //    Main-class-end
```