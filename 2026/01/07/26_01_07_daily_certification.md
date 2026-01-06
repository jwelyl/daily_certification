# 26_01_07_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1833 고속철도 설계하기

[1833번: 고속철도 설계하기](http://boj.ma/1833/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Collections;

public class Main {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringBuilder sb = new StringBuilder();
        int n = Integer.parseInt(br.readLine());
        int[] parents = new int[n];
        int dsCnt = n;
        
        List<Edge> edgeList = new ArrayList<>();
        List<Edge> addedList = new ArrayList<>();
        int answer = 0;
        
        for(int v = 0; v < n; v++)
            parents[v] = v;
        
        for(int v1 = 0; v1 < n; v1++) {
            StringTokenizer st = new StringTokenizer(br.readLine());
            for(int v2 = 0; v2 < n; v2++) {
                int cost = Integer.parseInt(st.nextToken());
                
                if(v1 < v2) {
                    if(cost < 0) {    //    이미 설치된 간선
                        answer -= cost;
                        
                        if(union(v1, v2, parents))
                            dsCnt--;
                    }
                    else
                        edgeList.add(new Edge(v1, v2, cost));
                }
            }
        }
        
        Collections.sort(edgeList);    //    간선 비용 오름차순으로 정렬
        
        for(int i = 0; i < edgeList.size() && dsCnt > 1; i++) {
            Edge edge = edgeList.get(i);
            int v1 = edge.v1;
            int v2 = edge.v2;
            int cost = edge.cost;
            
            if(union(v1, v2, parents)) {
                answer += cost;
                dsCnt--;
                addedList.add(edge);
            }    
        }
        
        sb.append(answer).append(" ").append(addedList.size()).append("\n");
        for(Edge edge : addedList)
            sb.append(edge.v1 + 1).append(" ").append(edge.v2 + 1).append("\n");
        
        System.out.print(sb);
    }    //    main-end
    
    private static int find(int v, int[] parents) {
        return v == parents[v] ? v : (parents[v] = find(parents[v], parents));
    }
    
    private static boolean union(int v1, int v2, int[] parents) {
        v1 = find(v1, parents);
        v2 = find(v2, parents);
        
        if(v1 == v2)
            return false;
        
        parents[v2] = v1;
        return true;
    }
    
    private static class Edge implements Comparable<Edge> {
        public int v1;
        public int v2;
        public int cost;
        
        public Edge(int v1, int v2, int cost) {
            this.v1 = v1;
            this.v2 = v2;
            this.cost = cost;
        }
        
        @Override
        public int compareTo(Edge other) {
            return Integer.compare(this.cost, other.cost);
        }
    }
}    //    Main-class-end
```

### BOJ 29728 실버와 소수는 둘다 S로 시작한다

[29728번: 실버와 소수는 둘다 S로 시작한다](http://boj.ma/29728/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Deque;

public class Main {
  private static final int MAX = 5_000_000;

  public static void main(String[] args) throws IOException {
    boolean[] sieve = init(); //  sieve[num] = false일 경우, num은 prime

    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    int n = Integer.parseInt(br.readLine());

    boolean reverse = false;  //  true일 경우, 가장 앞에 넣고 빼기, false일 경우 뒤에 넣고 빼기
    Deque<Character> deque = new ArrayDeque<>();

    int bCnt = 0;
    int sCnt = 0;

    for (int turn = 1; turn <= n; turn++) {
      if (!sieve[turn]) {  //  소수번째 턴일 경우
        if (reverse) {
          char ch = deque.peekFirst();

          if (ch == 'B') {
            deque.pollFirst();
            bCnt--;
            deque.offerFirst('S');
            deque.offerFirst('S');
            sCnt += 2;
          } else {
            deque.offerFirst('S');
            sCnt++;
          }
        } else {
          char ch = deque.peekLast();

          if (ch == 'B') {
            deque.pollLast();
            bCnt--;
            deque.offerLast('S');
            deque.offerLast('S');
            sCnt += 2;
          } else {
            deque.offerLast('S');
            sCnt++;
          }
        }

        reverse = !reverse; //  뒤집기
      } else {
        if (reverse) {
          deque.offerFirst('B');
        } else {
          deque.offerLast('B');
        }
        bCnt++;
      }
    }

    System.out.println(bCnt + " " + sCnt);
  } // main-end

  private static boolean[] init() {
    boolean[] sieve = new boolean[MAX + 1];

    sieve[1] = true;

    for (int num = 2; num * num <= MAX; num++) {
      if (!sieve[num]) {
        for (int mult = num * num; mult <= MAX; mult += num) {
          sieve[mult] = true;
        }
      }
    }

    return sieve;
  }
} // Main-class-end
```