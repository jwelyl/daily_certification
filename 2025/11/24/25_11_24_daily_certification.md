# 25_11_24_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 26086 어려운 스케줄링

[26086번: 어려운 스케줄링](http://boj.ma/26086/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Deque;
import java.util.ArrayDeque;
import java.util.Collections;

public class Main {
  private static final int NONE = Integer.MAX_VALUE;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    st.nextToken();                              //    업무 번호 최댓값
    int q = Integer.parseInt(st.nextToken());    //    쿼리 개수
    int k = Integer.parseInt(st.nextToken());    //    q개 쿼리 처리 후 k번째로 처리하게 될 업무

    List<Integer> processList = new ArrayList<>();        //    업무 리스트
    Deque<Integer> processDeque = new ArrayDeque<>();
    List<Query> queryList = new ArrayList<>();            //    쿼리 리스트
    boolean isFront = true;          //    true일 경우, 가장 나중에 들어온 업무부터 처리, false일 경우 먼저 들어온 업무부터 처리
    int lastQuery1Idx = NONE;     //    가장 마지막에 나타나는 쿼리 1 위치

    int answer = 0;

    for(int i = 0; i < q; i++) {
      st = new StringTokenizer(br.readLine());
      int type = Integer.parseInt(st.nextToken());
      int pId = type == 0 ? Integer.parseInt(st.nextToken()) : NONE;

      if(type == 1)
        lastQuery1Idx = i;

      queryList.add(new Query(type, pId));
    }

    if(lastQuery1Idx == NONE) { //  정렬이 없을 경우
      for(int i = 0; i < q; i++) {
        if(queryList.get(i).type == 0) {  //  삽입일 경우
          if(isFront)
            processDeque.offerFirst(queryList.get(i).pId);
          else
            processDeque.offerLast(queryList.get(i).pId);
        }
        else  //  순서 뒤집기일 경우
          isFront = !isFront;
      }
    }
    else {  //  정렬이 최소 한번 있을 경우
      //  정렬 전 데이터 삽입
      for(int i = 0; i < lastQuery1Idx; i++) {
        if(queryList.get(i).type == 0)  //  삽입일 경우
          processList.add(queryList.get(i).pId);
      }

      //  정렬하기
      Collections.sort(processList);
      for(int i = 0; i < processList.size(); i++)
        processDeque.offerLast(processList.get(i));
      isFront = true;

      //  정렬 후 데이터 삽입
      for(int i = lastQuery1Idx + 1; i < q; i++) {
        if(queryList.get(i).type == 0) {
          if(isFront)
            processDeque.offerFirst(queryList.get(i).pId);
          else
            processDeque.offerLast(queryList.get(i).pId);
        }
        else
          isFront = !isFront;
      }
    }

    for(int i = 0; i < k; i++) {
      if(isFront)
        answer = processDeque.pollFirst();
      else
        answer = processDeque.pollLast();
    }

    System.out.println(answer);
  }    //    main-end

  private static class Query {
    public int type;
    public int pId;

    public Query(int type, int pId) {
      this.type = type;
      this.pId = pId;
    }
  }
}    //    Main-class-end
```