# 26_04_03_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 10775 공항

[10775번: 공항](http://boj.ma/10775/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;

public class Main {
  private static final int CLOSED = 0;    //    공항 폐쇄

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    int g = Integer.parseInt(br.readLine()); //    공항 게이트 수
    int p = Integer.parseInt(br.readLine()); //    비행기 수

    int answer = 0;

    int[] parents = new int[g + 1];
    for(int v = 1; v <= g; v++)
      parents[v] = v;

    for(int i = 1; i <= p; i++) {
      int target = Integer.parseInt(br.readLine());    //   p번째 비행기가 도킹하려는 게이트 번호

      int gate = find(target, parents);	//	실제로 도킹할 수 있는 게이트 번호

      if(gate == CLOSED)	//	도킹할 수 없다면 공항 폐쇄
        break;

      answer++;	//	도킹할 수 있는 비행기 1개 증가
      union(gate, gate - 1, parents);
    }

    System.out.println(answer);
  }    //    main-end

  private static int find(int v, int[] parents) {
    return v == parents[v] ? v : (parents[v] = find(parents[v], parents));
  }

  private static void union(int v1, int v2, int[] parents) {
    v1 = find(v1, parents);
    v2 = find(v2, parents);

    parents[v1] = v2;
  }
}    //    Main-class-end
```