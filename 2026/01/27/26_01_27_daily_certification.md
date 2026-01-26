# 26_01_27_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 32387 충전하기

[32387번: 충전하기](http://boj.ma/32387/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.TreeSet;

public class Main {
  private static final int NONE = 0;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    StringTokenizer st = new StringTokenizer(br.readLine());

    int n = Integer.parseInt(st.nextToken()); //  포트 개수
    int q = Integer.parseInt(st.nextToken()); //  질의 개수

    //  사용되지 않은 포트 집합
    TreeSet<Integer> unusedPortSet = new TreeSet<>();
    //  time[port] : port에 최근에 전자기기 꽂힌 시간
    int[] time = new int[n + 1];

    for(int port = 1; port <= n; port++)
      unusedPortSet.add(port);

    for(int i = 1; i <= q; i++) {
      st = new StringTokenizer(br.readLine());
      int query = Integer.parseInt(st.nextToken());
      int option = Integer.parseInt(st.nextToken());

      //  option번째 포트가 비었을 경우, 해당 포트에 전자기기 꽂기
      //  option번째 포트가 비어있지 않을 경우, option보다 크면서 비어있는 포트 중 최소 포트에 전자기기 꽂기
      //  꽂았을 경우 포트 번호를, 꽂을 수 있는 포트가 없으면 -1
      if(query == 1) {
        Integer port = unusedPortSet.ceiling(option); //  option 이상의 포트 중 꽂을 수 있는 가장 작은 포트 번호

        if(port == null)  //  그런 포트가 없을 경우
          sb.append("-1\n");
        else {  //  그런 포트가 있을 경우
          sb.append(port).append("\n"); //  해당 포트 출력
          unusedPortSet.remove(port);   //  사용 가능한 포트에서 해당 포트 제거
          time[port] = i;               //  사용중인 포트에 해당 포트 꽂은 시간 기록
        }
      }
      //  option번째 포트가 비었을 경우, -1
      //  option번째 포트가 비어있지 않을 경우, 해당 포트에 꽂은 전자기기 꽂힌 시간 반환 (해당 전자기기는 뽑기)
      else {
        if(!unusedPortSet.contains(option)) { //  해당 포트가 사용 중일 경우
          sb.append(time[option]).append("\n");  //  해당 포트 꽂힌 시간 출력
          time[option] = NONE;
          unusedPortSet.add(option);    //  해당 포트 반납
        }
        else
          sb.append("-1\n");
      }
    }

    System.out.print(sb);
  } //	main-end
} //	Main-class-end
```