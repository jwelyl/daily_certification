# 26_01_13_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1893 시저 암호

[1893번: 시저 암호](http://boj.ma/1893/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    int t = Integer.parseInt(br.readLine());  //	테스트 케이스 개수

    for (int tc = 0; tc < t; tc++) {
      List<Integer> answerList = new ArrayList<>();

      //	알파벳 순서, 원문, 암호문
      char[] orders = br.readLine().toCharArray();
      char[] pattern = br.readLine().toCharArray();
      char[] text = br.readLine().toCharArray();

      Map<Character, Character> map = new HashMap<>();

      for (int i = 0; i < orders.length; i++)
        map.put(orders[i], orders[(i + 1) % orders.length]);

      //	A의 길이만큼 shift함
      for (int i = 0; i < orders.length; i++) {
        if (i >= 1) {    //	1번 이상 shift한 경우
          for (int k = 0; k < pattern.length; k++)
            pattern[k] = map.get(pattern[k]);
        }

        List<Integer> kmpResult = kmp(text, pattern);    //	S에서 W를 찾은 결과

        if (kmpResult.size() != 1) //	유일하게 매칭되지 않을 경우
          continue;

        answerList.add(i);    //	i번 shift했을 경우 유일하게 찾을 수 있음
      }

      if (answerList.isEmpty())
        sb.append("no solution\n");
      else if (answerList.size() == 1)
        sb.append("unique: ").append(answerList.get(0)).append("\n");
      else {
        sb.append("ambiguous: ");
        for (int shift : answerList)
          sb.append(shift).append(" ");
        sb.append("\n");
      }
    }

    System.out.print(sb);
  } //    main-end

  private static int[] makeFailure(char[] pattern) {
    int len = pattern.length;
    int[] failure = new int[len];

    int idx = 0;
    for (int i = 1; i < len; i++) {
      //	idx = 1 이후, 연속으로 더 일치하지 않으면, idx = failure[idx - 1]로 돌려줌.
      while (idx > 0 && pattern[i] != pattern[idx]) {
        idx = failure[idx - 1];
      }

      if (pattern[i] == pattern[idx]) {
        idx++;
        failure[i] = idx;
      }
    }

    return failure;
  }

  //	text에서 pattern이 나타나는 index 모두 반환
  private static List<Integer> kmp(char[] text, char[] pattern) {
    int[] failure = makeFailure(pattern);
    int lenT = text.length;
    int lenP = pattern.length;
    int idx = 0;    //	현재 대응되는 문자 위치
    List<Integer> resList = new ArrayList<>();    //	패턴 발견 위치

    for (int i = 0; i < lenT; i++) {
      //	pattern의 idx번째 문자와 text의 i번째 문자가 불일치할 경우
      //	현재 pattern의 위치를 failure[idx - 1]로 옮겨서 다시 비교
      while (idx > 0 && text[i] != pattern[idx]) {
        idx = failure[idx - 1];
      }

      if (text[i] == pattern[idx]) {
        if (idx == lenP - 1) {    //	pattern을 찾은 경우
          resList.add(i - idx);
          idx = failure[idx];
        } else
          idx++;    //	다음 글자 비교해야 함
      }
    }

    return resList;
  }
} //    Main-class-end
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