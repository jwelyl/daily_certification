# 26_01_11_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 10256 돌연변이

[10256번: 돌연변이](http://boj.ma/10256/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    int t = Integer.parseInt(br.readLine());  //  테스트 케이스 개수

    for (int tc = 1; tc <= t; tc++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      int n = Integer.parseInt(st.nextToken()); //  DNA 문자열 길이
      int m = Integer.parseInt(st.nextToken()); //  마커 길이

      Trie trie = new Trie();

      String dna = br.readLine();     //  dna
      String marker = br.readLine();  //  마커

      for (int from = 0; from < m; from++) {
        for (int to = from; to < m; to++) {
          String head = from == 0 ? "" : marker.substring(0, from);
          String body = marker.substring(from, to + 1);
          String tail = to == m - 1 ? "" : marker.substring(to + 1);

          body = new StringBuilder(body).reverse().toString();

          trie.insertWord(head + body + tail);
        }
      }

      trie.makeFailFunction();

      sb.append(trie.ahoCorasick(dna)).append("\n");
    }

    System.out.print(sb);
  }  //  main-end

  private static class TrieNode {
    private final TrieNode[] children = new TrieNode[4];
    private TrieNode fail;  //  실패 링크
    private boolean isEnd = false;
  }

  private static class Trie {
    private final TrieNode root = new TrieNode();

    public void insertWord(String word) {
      TrieNode cur = this.root;

      for (int i = 0; i < word.length(); i++) {
        char ch = word.charAt(i);
        int idx = ch == 'A' ? 0 : (ch == 'C' ? 1 : (ch == 'G' ? 2 : 3));

        if (cur.children[idx] == null)
          cur.children[idx] = new TrieNode();

        cur = cur.children[idx];
      }

      cur.isEnd = true;
    }

    public void makeFailFunction() {
      Queue<TrieNode> queue = new ArrayDeque<>();
      this.root.fail = this.root; //  루트의 실패 링크는 자기 자신
      queue.offer(this.root);

      while (!queue.isEmpty()) {
        TrieNode cur = queue.poll();

        for (int idx = 0; idx < 4; idx++) {
          TrieNode next = cur.children[idx];

          if(next == null)
            continue;

          if (cur == this.root)  //  루트의 바로 자식 노드의 실패 링크는 루트 노드로
            next.fail = this.root;
          else {  //  부모를 따라 가면서 실패 링크 찾기
            TrieNode failNode = cur.fail; //  부모의 실패 링크

            while (failNode != this.root && failNode.children[idx] == null)
              failNode = failNode.fail;

            if (failNode.children[idx] != null)
              failNode = failNode.children[idx];

            next.fail = failNode;
          }

          if (next.fail.isEnd)
            next.isEnd = true;

          queue.offer(next);
        }
      }
    }

    public int ahoCorasick(String text) {
      TrieNode cur = this.root;
      int cnt = 0;

      for (int i = 0; i < text.length(); i++) {
        char ch = text.charAt(i);
        int idx = ch == 'A' ? 0 : (ch == 'C' ? 1 : (ch == 'G' ? 2 : 3));

        while (cur != this.root && cur.children[idx] == null)
          cur = cur.fail;

        if (cur.children[idx] != null)
          cur = cur.children[idx];

        if (cur.isEnd)
          cnt++;
      }

      return cnt;
    }
  }
} //  Main-class-end
```

### BOJ 34896 폭탄의 악마

[34896번: 폭탄의 악마](http://boj.ma/34896/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st;
    int n = Integer.parseInt(br.readLine());  //  폭탄 수
    int limit = 0;  //  비용 한계
    List<Bomb> bombList = new ArrayList<>();

    st = new StringTokenizer(br.readLine());
    for(int i = 0;i < n; i++) {
      bombList.add(new Bomb());
      bombList.get(i).pos = Integer.parseInt(st.nextToken());
    }

    st = new StringTokenizer(br.readLine());
    for(int i = 0;i < n; i++)
      bombList.get(i).cost = Integer.parseInt(st.nextToken());

    limit = Integer.parseInt(br.readLine());
    Collections.sort(bombList);

    System.out.println(parametricSearch(n, limit, bombList));
  } //  main-end

  private static int parametricSearch(int n, int limit, List<Bomb> bombList) {
    int start = 1;
    int end = bombList.get(n - 1).pos - bombList.get(0).pos;
    int res = end;

    while(start <= end) {
      int mid = (start + end) / 2;  //  힘을 mid만큼만 사용
      int sum = getSum(n, bombList, mid);

      if(sum <= limit) {  //  한계 비용 안에서 모든 폭탄 터뜨릴 수 있을 경우
        res = mid;
        end = mid - 1;    //  더 적은 힘으로 가능한지 체크
      }
      else                //  한계 비용 안에서 모든 폭탄 터뜨릴 수 없을 경우
        start = mid + 1;  //  더 큰 힘으로 가능한지 체크
    }

    return res;
  }

  private static int getSum(int n, List<Bomb> bombList, int mid) {
    int sum = 0;

    int chunkCost = Integer.MAX_VALUE;
    for(int i = 0; i < n - 1; i++) {
      Bomb cur = bombList.get(i);
      Bomb next = bombList.get(i + 1);

      if(next.pos - cur.pos <= mid) //  폭탄 cur과 next가 같이 터질 수 있을 경우
        chunkCost = Math.min(chunkCost, Math.min(cur.cost, next.cost));
      else {  //  폭탄 cur과 next가 같이 터질 수 없을 경우
        chunkCost = Math.min(chunkCost, cur.cost);
        sum += chunkCost;
        chunkCost = Integer.MAX_VALUE;
      }
    }

    sum += Math.min(chunkCost, bombList.get(n - 1).cost);
    return sum;
  }

  private static class Bomb implements Comparable<Bomb> {
    public int pos;   //  폭탄 위치
    public int cost;  //  폭발 비용

    @Override
    public int compareTo(Bomb other) {
      return Integer.compare(pos, other.pos);
    }
  }
} //  Main-class-end
```

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st;
    int n = Integer.parseInt(br.readLine());  //  폭탄 수
    int limit = 0;  //  비용 한계
    List<Bomb> bombList = new ArrayList<>();

    st = new StringTokenizer(br.readLine());
    for(int i = 0; i < n; i++) {
      bombList.add(new Bomb());
      bombList.get(i).pos = Integer.parseInt(st.nextToken());
    }

    st = new StringTokenizer(br.readLine());
    for(int i = 0; i < n; i++)
      bombList.get(i).cost = Integer.parseInt(st.nextToken());

    limit = Integer.parseInt(br.readLine());
    //  폭탄 위치 순으로 오름차순 정렬
    Collections.sort(bombList);

    System.out.println(parametricSearch(n, limit, bombList));
  } //  main-end

  private static int find(int x, int[] parents) {
    return x == parents[x] ? x : (parents[x] = find(parents[x], parents));
  }

  private static void union(int x, int y, int[] parents, List<Bomb> bombList) {
    x = find(x, parents);
    y = find(y, parents);

    if(x != y) {
      Bomb bombX = bombList.get(x);
      Bomb bombY = bombList.get(y);

      //  비용이 작은 폭탄이 부모
      if(bombX.cost < bombY.cost)
        parents[y] = x;
      else
        parents[x] = y;
    }
  }

  private static int parametricSearch(int n, int limit, List<Bomb> bombList) {
    int start = 1;
    int end = bombList.get(n - 1).pos - bombList.get(0).pos;
    int res = end;

    int[] parents = new int[n];

    while(start <= end) {
      int mid = (start + end) / 2;  //  힘을 mid만큼만 사용
      int sum = getSum(n, parents, bombList, mid);

      if(sum <= limit) {  //  한계 비용 안에서 모든 폭탄 터뜨릴 수 있을 경우
        res = mid;
        end = mid - 1;    //  더 적은 힘으로 가능한지 체크
      }
      else                //  한계 비용 안에서 모든 폭탄 터뜨릴 수 없을 경우
        start = mid + 1;  //  더 큰 힘으로 가능한지 체크
    }

    return res;
  }

  private static int getSum(int n, int[] parents, List<Bomb> bombList, int mid) {
    int sum = 0;
    for(int i = 0; i < n; i++)
      parents[i] = i;

    for(int i = 0; i < n; i++) {
      Bomb cur = bombList.get(i);
      Bomb next = i + 1 < n ? bombList.get(i + 1) : null;

      if(next != null && next.pos - cur.pos <= mid) //  현재 폭탄이 다음 폭탄과 함께 터질 수 있을 경우
        union(i, i + 1, parents, bombList); //  함께 터지는 한 그룹으로 묶기
      else {  //  현재 폭탄이 다음 폭탄과 함께 터질 수 없는 경우
        int idx = find(i, parents); //  현재 폭탄이 속한 그룹 중 가장 비용이 작은 폭탄 인덱스
        sum += bombList.get(idx).cost;
      }
    }
    return sum;
  }

  private static class Bomb implements Comparable<Bomb> {
    public int pos;   //  폭탄 위치
    public int cost;  //  폭발 비용

    @Override
    public int compareTo(Bomb other) {
      return Integer.compare(pos, other.pos);
    }
  }
} //  Main-class-end
```

### BOJ 12842 튀김 소보루

[12842번: 튀김 소보루](http://boj.ma/12842/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.PriorityQueue;

public class Main {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());
        int n = Integer.parseInt(st.nextToken()) - Integer.parseInt(st.nextToken());    //    먹어버린 튀소 개수
        
        int m = Integer.parseInt(br.readLine());    //    사람 수
        int[] times = new int[m + 1];               //    튀소 먹는데 걸리는 시간
        
        PriorityQueue<Eat> pq = new PriorityQueue<>();
        int cnt = 0;        //    튀소 먹은 개수
        int answer = 0;     //    n번째 튀소 가져간 사람 번호
        
        for(int num = 1; num <= m; num++) {
            times[num] = Integer.parseInt(br.readLine());
            pq.offer(new Eat(num, 0));    //    처음엔 m명 사람이 모두 0초에 튀소를 가져감
        }
        
        while(cnt < n) {
            Eat eat = pq.poll();
            answer = eat.num;
            cnt++;
            eat.time += times[eat.num];
            pq.offer(eat);
        }
        
        System.out.println(answer);
    }    //    main-end
    
    private static class Eat implements Comparable<Eat> {
        public int num;    //    사람 번호
        public int time;   //    먹기 시작하는 시간
        
        public Eat(int num, int time) {
            this.num = num;
            this.time = time;
        }
        
        @Override
        public int compareTo(Eat other) {
            int res = Integer.compare(this.time, other.time);    //    더 빨리 먹기 시작할 수록 튀소를 먼저 가져감

            if(res == 0)    //    먹기 시작하는 시간이 같을 경우
                res = Integer.compare(this.num, other.num);    //    번호가 작을 수록 튀소를 먼저 가져감
            
            return res;
        }
    }
}    //    Main-class-end

```