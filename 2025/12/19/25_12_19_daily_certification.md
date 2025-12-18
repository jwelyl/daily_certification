# 25_12_19_daily_certification

# Problem Solving (Algorithm & SQL)

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