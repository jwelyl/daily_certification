# 25_11_30_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 3045 이중 연결 리스트

[3045번: 이중 연결 리스트](http://boj.ma/3045/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Deque;
import java.util.ArrayDeque;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    StringTokenizer st = new StringTokenizer(br.readLine());
      
    List<Integer> lis = new ArrayList<>();        //    lis 길이 구하기 위한 리스트
    Deque<Integer> stack = new ArrayDeque<>();   //    실제 lis에 포함되는 수 찾기 위한 스택
    int idx = 0;
      
    int n = Integer.parseInt(st.nextToken()); //  노드 개수
    int m = Integer.parseInt(st.nextToken()); //  연산 개수

    //  0번 노드 : head, n + 1번 노드 : tail
    //  prevs[i] : i번째 노드의 이전 노드 번호
    //  nexts[i] : i번째 노드의 다음 노드 번호
    int[] prevs = new int[n + 2];
    int[] nexts = new int[n + 2];
    int[] sequence = new int[n + 1];          //    m번의 조작 후의 노드 수열
    boolean[] isLis = new boolean[n + 1];     //    isLis[i] : i가 lis에 포함되면 true
    int[] indexArr = new int[n + 1];          //    실제 lis 구하기 위한 노드의 위치 저장
    
    int lisMax = -1;    //    lis에 포함된 수 중 가장 큰 수

    //  DLL 초기화
    nexts[0] = 1;
    for(int i = 1; i <= n; i++) {
      prevs[i] = i - 1;
      nexts[i] = i + 1;
    }
    prevs[n + 1] = n;

    for(int i = 0; i < m; i++) {
      st = new StringTokenizer(br.readLine());
      char opt = st.nextToken().charAt(0);
      int x = Integer.parseInt(st.nextToken());
      int y = Integer.parseInt(st.nextToken());

      //  x를 y 앞에 두기
      if(opt == 'A') {
        if(nexts[x] == y)  //  이미 x가 y 앞일 경우
          continue;

        int prevX = prevs[x];
        int nextX = nexts[x];

        nexts[prevX] = nextX;
        prevs[nextX] = prevX;

        int prevY = prevs[y];
        nexts[prevY] = x;
        nexts[x] = y;
        prevs[y] = x;
        prevs[x] = prevY;
      }
      //  x를 y 뒤에 두기
      else {
        if(prevs[x] == y) //  이미 x가 y 뒤일 경우
          continue;

        int prevX = prevs[x];
        int nextX = nexts[x];

        nexts[prevX] = nextX;
        prevs[nextX] = prevX;

        int nextY = nexts[y];
        prevs[nextY] = x;
        prevs[x] = y;
        nexts[x] = nextY;
        nexts[y] = x;
      }
    }

    for(int node = nexts[0], i = 1; node != n + 1; node = nexts[node], i++)
        sequence[i] = node;
    
    lis.add(Integer.MIN_VALUE);
    for(int i = 1; i <= n; i++) {
        if(sequence[i] > lis.get(lis.size() - 1)) {    	//	lis에 저장된 마지막 수보다 더 클 경우 lis에 추가, 길이 1 증가
            lis.add(sequence[i]); 
            indexArr[i] = lis.size() - 1;
        }
        else {
            idx = upperBound(sequence[i], lis);
            
            lis.set(idx, sequence[i]);
            indexArr[i] = idx;
        }
    }
      
    idx = lis.size() - 1;    //    찾으려는 증가 수열 인덱스
      
    for(int i = n; i >= 1; i--) {
        if(indexArr[i] == idx) {
            stack.offerLast(sequence[i]);
            idx--;
        }
    }

    while(!stack.isEmpty()) {
        int node = stack.pollLast();
        isLis[node] = true;
        lisMax = Math.max(lisMax, node);
    }

    sb.append(n - (lis.size() - 1)).append("\n");
    //    lisMax보다 작으면서 lis에 포함되지 않은 노드들을 내림차순으로 제자리에 배치
    for(int node = lisMax - 1; node >= 1; node--) {
        if(!isLis[node])
            sb.append("A ").append(node).append(" ").append(node + 1).append("\n");
    }
    //    lisMax보다 큰 lis에 포함되지 않은 노드들을 오름차순으로 제자리에 배치
    for(int node = lisMax + 1; node <= n; node++) {
        if(!isLis[node])
            sb.append("B ").append(node).append(" ").append(node - 1).append("\n");
    }

    System.out.print(sb);
  }    //    main-end

  private static int upperBound(int node, List<Integer> lis) {
    int start = 0;
    int end = lis.size() - 1;
    int res = end;

    while(start <= end) {
      int mid = (start + end) / 2;
      if(lis.get(mid) > node) {
        res = mid;
        end = mid - 1;
      }
      else
        start = mid + 1;
    }

    return res;
  }
}    //    Main-class-end
```