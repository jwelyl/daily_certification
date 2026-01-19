# 26_01_20_daily_certification

```
[#020 koreii] 데일리인증 20260120
1. CS
- OpenSearch
2. 알고리즘 (복습) 문제 풀이
- BOJ 20366 같이 눈사람 만들래? (Sorting, Two Pointer)
```

# Problem Solving (Algorithm & SQL)

### BOJ 20366 **같이 눈사람 만들래?**

[20366번: 같이 눈사람 만들래?](http://boj.ma/20366/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st;
    int n = Integer.parseInt(br.readLine());  	//	눈덩이 개수
    int[] snows = new int[n];                   //	눈덩이 높이

    List<Snowman> snowmenList = new ArrayList<>();	//	눈사람 리스트

    int answer = Integer.MAX_VALUE;	//	두 눈사람 키 차이의 최소

    st = new StringTokenizer(br.readLine());
    for(int i = 0; i < n; i++)
      snows[i] = Integer.parseInt(st.nextToken());

    Arrays.sort(snows);
    //	만들 수 있는 모든 눈사람 생성
    for(int i = 0; i < n - 1; i++) {
      for(int j = i + 1; j < n; j++)
        snowmenList.add(new Snowman(snows[i] + snows[j], i, j));
    }

    //	눈사람들을 키 순서대로 정렬
    Collections.sort(snowmenList);

    for(int i = 0; i < snowmenList.size() - 1; i++) {
      Snowman first =  snowmenList.get(i);		//	i번째 눈사람
      Snowman second = snowmenList.get(i + 1);	//	i+1번째 눈사람

      //	인접한 눈사람이 키 차이가 가장 적음
      //	두 눈사람을 만드는 데 같은 눈덩이가 쓰인 경우 두 개의 눈사람은 동시에 존재할 수 없음
      if(first.idx1 == second.idx1 || first.idx1 == second.idx2 || first.idx2 == second.idx1 || first.idx2 == second.idx2)
        continue;

      if(second.height - first.height < answer)
        answer = second.height - first.height;
    }

    System.out.println(answer);
  } //  main-end

  private static class Snowman implements Comparable<Snowman> {	//	눈사람
    public int height;	//	눈사람 높이(머리 눈덩이 높이 + 몸통 눈덩이 높이)
    public int idx1;	//	눈덩이 하나 인덱스
    public int idx2;	//	눈덩이 다른 하나 인덱스

    public Snowman(int height, int idx1, int idx2) {
      this.height = height;
      this.idx1 = idx1;
      this.idx2 = idx2;
    }

    @Override
    public int compareTo(Snowman other) {
      //  눈사람 높이 작은 순으로 정렬
      return Integer.compare(this.height, other.height);
    }
  }
} //  Main-class-end
```