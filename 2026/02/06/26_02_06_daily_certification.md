# 26_02_06_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 24041 성싶당 밀키트

[24041번: 성싶당 밀키트](http://boj.ma/24041/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Collections;

public class Main {
  private static final int MAX = 2_000_000_000;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken()); //    재료 수
    int g = Integer.parseInt(st.nextToken()); //    허용 가능한 세균 수
    int k = Integer.parseInt(st.nextToken()); //    제외할 수 있는 중요하지 않은 재료 수

    int[] sArr = new int[n];          //    sArr[i] : i번째 재료의 부패 속도
    int[] lArr = new int[n];          //    lArr[i] : i번째 재료의 유통기한
    boolean[] oArr = new boolean[n];  //    oArr[i] : i번째 재료가 중요하면 true

    for(int i = 0; i < n; i++) {
      st = new StringTokenizer(br.readLine());
      int s = Integer.parseInt(st.nextToken());
      int l = Integer.parseInt(st.nextToken());
      int o = Integer.parseInt(st.nextToken());

      sArr[i] = s;
      lArr[i] = l;
      if(o == 0)    //    중요한 재료면
        oArr[i] = true;
    }

    System.out.println(parametricSearch(n, g, k, sArr, lArr, oArr));
  }    //    main-end

  private static int parametricSearch(int n, int g, int k, int[] sArr, int[] lArr, boolean[] oArr) {
    int start = 0;
    int end = MAX;
    int res = 0;    //    구매 후 최대 res일까지 먹을 수 있음

    while(start <= end) {
      int mid = start + (end - start) / 2;    //    최대 mid일 지난 후에 먹을 수 있는지 확인

      if(ok(n, g, k, sArr, lArr, oArr, mid)) {                           //    mid일 지난 후에도 먹을 수 있을 경우
        res = mid;                          //    일단 mid 저장
        start = mid + 1;                    //    더 오랜 시간 지난 것도 먹을 수 있는지 확인
      }
      else                                    //    mid일 지난 후에는 먹을 수 없다면
        end = mid - 1;                      //    더 적은 시간 확인
    }

    return res;
  }

  //    day일 지난 후에 먹을 수 있나면 true
  private static boolean ok(int n, int g, int k, int[] sArr, int[] lArr, boolean[] oArr, int day) {
    long sum = 0;

    List<Long> list = new ArrayList<>();    //    뺄 수 있는 재료들에 의해 생기는 세균 수 리스트

    for(int i = 0; i < n; i++) {
      if(oArr[i])    //    중요한 재료일 경우
        sum += (long)sArr[i] * Math.max(1, day - lArr[i]);
      else    //    중요하지 않은 재료일 경우
        list.add((long)sArr[i] * Math.max(1, day - lArr[i]));
    }

    Collections.sort(list, Collections.reverseOrder());    //    세균 수 많은 순으로 정렬

    for(int i = 0; i < list.size(); i++) {
      if(i < k)        //    세균이 가장 많이 생기는 K개의 재료 제거
        continue;

      sum += list.get(i);
    }

    return sum <= g;
  }
}    //    Main-class-end
```