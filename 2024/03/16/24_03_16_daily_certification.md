# 24_03_16_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 4256 트리**

[4256번: 트리](https://www.acmicpc.net/problem/4256)

예제 2번을 기준으로 설명하겠다. 편의 상 루트 노드의 왼쪽 서브트리까지만 탐색하겠다.

![ex1_1.jpeg](24_03_16_daily_certification%20f6324f5b752e45728acffbaf2a95842d/ex1_1.jpeg)

preorder에서 가장 처음 방문한 노드가 루트 노드이다. inorder에서 루트 노드를 찾고 그 왼쪽이 왼쪽 서브트리를 inorder로 탐색한 결과이고, 오른쪽이 오른쪽 서브트리를 inorder로 탐색한 결과이다. 모든 노드의 값이 다르기 때문에 가능하다.

![ex1_2.jpeg](24_03_16_daily_certification%20f6324f5b752e45728acffbaf2a95842d/ex1_2.jpeg)

왼쪽 서브트리를 탐색할 때 이전 탐색에서 inorder에서 찾은 루트 노드 위치의 왼쪽 부분을 탐색 범위로 바꾼다. 현재 탐색하는 서브 트리의 왼쪽 서브트리의 루트노드는 preorder에서 현재 서브트리의 루트노드 바로 다음이고, 오른쪽 서브트리의 루트노드는 현재 서브트리의 루트노드 위치 + (왼쪽 서브트리 노드 개수)이다.

![ex1_3.jpeg](24_03_16_daily_certification%20f6324f5b752e45728acffbaf2a95842d/ex1_3.jpeg)

 현재 노드의 왼쪽 자식노드나 오른쪽 자식 노드가 없을 경우 postorder 시 start가 end보다 작게 되므로 탐색이 바로 끝난다. 그리고 해당 노드를 출력하면 된다.

![ex1_4.jpeg](24_03_16_daily_certification%20f6324f5b752e45728acffbaf2a95842d/ex1_4.jpeg)

그런 식으로 반복하면 된다.

![ex1_5.jpeg](24_03_16_daily_certification%20f6324f5b752e45728acffbaf2a95842d/ex1_5.jpeg)

![ex1_6.jpeg](24_03_16_daily_certification%20f6324f5b752e45728acffbaf2a95842d/ex1_6.jpeg)

![ex1_7.jpeg](24_03_16_daily_certification%20f6324f5b752e45728acffbaf2a95842d/ex1_7.jpeg)

이미 풀어본 문제지만 이 문제도 다시 풀어보자.

[2263번: 트리의 순회](https://www.acmicpc.net/problem/2263)

**코드**

```java
import java.io.*;
import java.util.StringTokenizer;

public class Main {
  private final static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static StringTokenizer tokens;
  private final static StringBuilder sb = new StringBuilder();

  private static int tc;    //  테스트 케이스 개수

  private static int n;    //  트리 노드 개수
  private static int[] preorder;  //  전위 순회 결과
  private static int[] inorder;   //  중위 순회 겨과

  public static void main(String[] args) throws IOException {
    tc = Integer.parseInt(br.readLine());

    for(int t = 0; t < tc; t++) {
      n = Integer.parseInt(br.readLine());

      preorder = new int[n];
      tokens = new StringTokenizer(br.readLine());
      for(int i = 0; i < n; i++)
        preorder[i] = Integer.parseInt(tokens.nextToken());

      inorder = new int[n];
      tokens = new StringTokenizer(br.readLine());
      for(int i = 0; i < n; i++)
        inorder[i] = Integer.parseInt(tokens.nextToken());

      postorder(0, 0, n - 1);
      sb.append("\n");
    }

    System.out.print(sb);
  }   //  main-end

  private static void postorder(int rootIdx, int start, int end) {
    if(start > end)
      return;

    for(int idx = start; idx <= end; idx++) {
      if(preorder[rootIdx] == inorder[idx]) { //  inorder에서 루트 노드 찾기
        postorder(rootIdx + 1, start, idx - 1); //  왼쪽 서브트리 순회
        postorder(rootIdx + idx + 1 - start, idx + 1, end); //  오른쪽 서브트리 순회
        sb.append(preorder[rootIdx]).append(" ");
      }
    }
  }

}   //  Main-class-end
```