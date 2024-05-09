# 24_05_09_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 문제 추천 시스템 1**

[https://www.codetree.ai/missions/8/problems/problem-recommendation-system-1/description](https://www.codetree.ai/missions/8/problems/problem-recommendation-system-1/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/problem-recommendation-system-1/description)

**코드**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.HashMap;
import java.util.Map;
import java.util.StringTokenizer;
import java.util.TreeSet;

public class Main {
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static final StringBuilder sb = new StringBuilder();
  private static StringTokenizer tokens;

  private static int n; //  문제 수
  private static int m; //  명령어 수

  private static final TreeSet<Problem> treeSet = new TreeSet<>();
  private static final Map<Integer, Integer> map = new HashMap<>(); //  Key : 문제 번호, Value : 문제 난이도

  public static void main(String[] args) throws IOException {
    n = Integer.parseInt(br.readLine());

    for(int i = 0; i < n; i++) {
      tokens = new StringTokenizer(br.readLine());
      int num = Integer.parseInt(tokens.nextToken());
      int level = Integer.parseInt(tokens.nextToken());

      treeSet.add(new Problem(num, level));
    }

    m = Integer.parseInt(br.readLine());

    for(int i = 0; i < m; i++) {
      tokens = new StringTokenizer(br.readLine());
      String cmd = tokens.nextToken();

      Problem out = null;
      int num;    //  문제 번호
      int level;  //  문제 레벨

      switch(cmd) {
        case "rc":
          int x = Integer.parseInt(tokens.nextToken());

          if(x == 1)
            out = treeSet.first();
          else
            out = treeSet.last();

          sb.append(out.num).append("\n");

          break;
        case "ad":
          num = Integer.parseInt(tokens.nextToken());     //  추가할 문제 번호
          level = Integer.parseInt(tokens.nextToken());   //  추가할 문제의 레벨

          int prevLevel = map.getOrDefault(num, -1);  //  해당 문제가 이미 존재하는 문제일 경우 이전 난이도

          if(prevLevel != -1)  //  해당 문제가 이미 존재할 경우
            treeSet.remove(new Problem(num, prevLevel));  //  이전 문제 제거

          treeSet.add(new Problem(num, level));         //  문제 삽입(이미 존재할 경우 레벨 변경해서 다시 삽입)
          map.put(num, level);
          break;
        case "sv":
          num = Integer.parseInt(tokens.nextToken());     //  삭제할 문제 번호
          level = Integer.parseInt(tokens.nextToken());   //  삭제할 문제의 레벨

          treeSet.remove(new Problem(num, level));
          map.remove(num);
          break;
      }
    }

    System.out.print(sb);
  } // main-end

  private static class Problem implements Comparable<Problem> {
    int num;    //  문제 번호
    int level;  //  문제 레벨

    public Problem(int num, int level) {
      this.num = num;
      this.level = level;
    }

    @Override
    public int compareTo(Problem prob) {
      int ret = Integer.compare(prob.level, this.level);  //  레벨이 높은 문제 먼저

      if(ret == 0)  //  두 문제 레벨이 같을 경우
        ret = Integer.compare(prob.num, this.num);  //  문제 번호가 큰 문제 먼저

      return ret;
    }

    @Override
    public String toString() {
      return "Problem{" +
          "num=" + num +
          ", level=" + level +
          '}';
    }
  }
} //  Main-class-end

```

**CO{)E TREE 최대로 연속한 숫자**

[https://www.codetree.ai/missions/8/problems/maximum-consecutive-number/description](https://www.codetree.ai/missions/8/problems/maximum-consecutive-number/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/maximum-consecutive-number/description)

**코드**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Scanner;
import java.util.StringTokenizer;
import java.util.TreeSet;

public class Main {
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static final StringBuilder sb = new StringBuilder();
  private static StringTokenizer tokens;

  private static int n; //  0부터 n까지의 수
  private static int m; //  m개의 수 제거
  private static final TreeSet<Integer> deletedNums = new TreeSet<>(); //  지워진 수 모음
  private static TreeSet<Segment> segments = new TreeSet<>();  // (길이, 시작 숫자, 끝 숫자)

  public static void main(String[] args) throws IOException {
    tokens = new StringTokenizer(br.readLine());
    n = Integer.parseInt(tokens.nextToken());
    m = Integer.parseInt(tokens.nextToken());

    deletedNums.add(-1);
    deletedNums.add(n + 1); //  null 나오지 않게 처리
    segments.add(new Segment(n + 1, -1, n + 1));

    tokens = new StringTokenizer(br.readLine());
    for(int i = 0; i < m; i++) {
      int delNum = Integer.parseInt(tokens.nextToken());  //  지울 수

      deletedNums.add(delNum);

      int left = deletedNums.lower(delNum);  //  지워진 수들 중 이번에 지워진 수보다 작으면서 가장 큰 수
      int right = deletedNums.higher(delNum); //  지워진 수들 중 이번에 지워진 수보다 크면서 가장 작은 수
      
      segments.remove(new Segment(right - left - 1, left, right));
      segments.add(new Segment(delNum - left - 1, left, delNum));
      segments.add(new Segment(right - delNum - 1, delNum, right));

      // y가 추가된 후로 구간 중 가장 긴 구간을 찾아 출력합니다.
      System.out.println(segments.first().length);
    }
  } // main-end

  private static class Segment implements Comparable<Segment> {
    int length;
    int start;
    int end;

    public Segment(int length, int start, int end) {
      this.length = length;
      this.start = start;
      this.end = end;
    }

    @Override
    public int compareTo(Segment t) {
      if(this.length != t.length)
        return t.length - this.length;  // length 기준 내림차순 정렬
      else if(this.start != t.start)
        return this.start - t.start;      // start 기준 오름차순 정렬
      else
        return this.end - t.end;      // end 기준 오름차순 정렬
    }
  }
} // Main-class-end
```