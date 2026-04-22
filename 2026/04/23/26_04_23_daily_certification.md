# 26_04_23_daily_certification

# Problem Solving (Algorithm & SQL)

[](http://boj.ma/14003/t)

### BOJ 14003 가장 긴 증가하는 부분 수열 5

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    StringTokenizer st;
    int n = Integer.parseInt(br.readLine());

    int[] sequence = new int[n + 1];                //	수열 배열
    int[] indexArr = new int[n + 1];                //	수열의 각 수의 위치 저장
    List<Integer> lis = new ArrayList<>();	//	LIS 크기 나타낼 리스트

    Deque<Integer> stack = new ArrayDeque<>();	//	경로 저장할 스택
    int idx;
    lis.add(Integer.MIN_VALUE);

    st = new StringTokenizer(br.readLine());
    for(int i = 1; i <= n; i++)
      sequence[i] = Integer.parseInt(st.nextToken());

    for(int i = 1; i <= n; i++) {
      if(sequence[i] > lis.get(lis.size() - 1)) {	//	lis에 저장된 마지막 수보다 더 클 경우 lis에 추가, 길이 1 증가됨
        lis.add(sequence[i]);
        indexArr[i] = lis.size() - 1;	//	lis의 가장 뒤에 위치
      }
      else {
        int pos = binarySearch(sequence[i], lis);

        if(pos == -1)
          continue;
        lis.set(pos, sequence[i]);
        indexArr[i] = pos;
      }
    }

    sb.append(lis.size() - 1).append("\n");

    idx = lis.size() - 1;	//	찾으려는 증가 수열 인덱스

    for(int i = n; i >= 1; i--) {
      if(indexArr[i] == idx) {		//	찾으려는 인덱스와 현재 수의 증가 수열에서의 인덱스가 같을 경우
        stack.offerLast(sequence[i]);	//	스택에 경로 저장
        idx--;						//	찾으려는 인덱스 1 감소
      }
    }

    while(!stack.isEmpty())
      sb.append(stack.pollLast()).append(" ");
    System.out.println(sb);
  } //  main-end

  //	num 바로 오른쪽의 더 큰 수의 index 바로 전 index를 return함
  private static int binarySearch(int num, List<Integer> lis) {
    int start = 0;
    int end = lis.size() - 1;

    while(start < end) {
      int mid = (start + end) / 2;
      if(lis.get(mid) < num)	//	num이 중간 수보다 더 클 경우 더 오른쪽에서 찾아야 함
        start = mid + 1;
      else if(lis.get(mid) >= num) {	//	num이 중간 수보다 더 작거나 같을 경우 일단 index 저장하고 더 왼쪽에서 찾아보기
        end = mid;
      }
    }

    return end;
  }
} //  Main-class-end
```