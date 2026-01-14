# 26_01_13_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1893 시저 암호

[1893번: 시저 암호](http://boj.ma/1893/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    int t = Integer.parseInt(br.readLine());  //	테스트 케이스 개수

    for (int tc = 0; tc < t; tc++) {
      List<Integer> answerList = new ArrayList<>();

      //	알파벳 순서, 원문, 암호문
      char[] orders = br.readLine().toCharArray();
      char[] pattern = br.readLine().toCharArray();
      char[] text = br.readLine().toCharArray();

      Map<Character, Character> map = new HashMap<>();

      for (int i = 0; i < orders.length; i++)
        map.put(orders[i], orders[(i + 1) % orders.length]);

      //	A의 길이만큼 shift함
      for (int i = 0; i < orders.length; i++) {
        if (i >= 1) {    //	1번 이상 shift한 경우
          for (int k = 0; k < pattern.length; k++)
            pattern[k] = map.get(pattern[k]);
        }

        List<Integer> kmpResult = kmp(text, pattern);    //	S에서 W를 찾은 결과

        if (kmpResult.size() != 1) //	유일하게 매칭되지 않을 경우
          continue;

        answerList.add(i);    //	i번 shift했을 경우 유일하게 찾을 수 있음
      }

      if (answerList.isEmpty())
        sb.append("no solution\n");
      else if (answerList.size() == 1)
        sb.append("unique: ").append(answerList.get(0)).append("\n");
      else {
        sb.append("ambiguous: ");
        for (int shift : answerList)
          sb.append(shift).append(" ");
        sb.append("\n");
      }
    }

    System.out.print(sb);
  } //    main-end

  private static int[] makeFailure(char[] pattern) {
    int len = pattern.length;
    int[] failure = new int[len];

    int idx = 0;
    for (int i = 1; i < len; i++) {
      //	idx = 1 이후, 연속으로 더 일치하지 않으면, idx = failure[idx - 1]로 돌려줌.
      while (idx > 0 && pattern[i] != pattern[idx]) {
        idx = failure[idx - 1];
      }

      if (pattern[i] == pattern[idx]) {
        idx++;
        failure[i] = idx;
      }
    }

    return failure;
  }

  //	text에서 pattern이 나타나는 index 모두 반환
  private static List<Integer> kmp(char[] text, char[] pattern) {
    int[] failure = makeFailure(pattern);
    int lenT = text.length;
    int lenP = pattern.length;
    int idx = 0;    //	현재 대응되는 문자 위치
    List<Integer> resList = new ArrayList<>();    //	패턴 발견 위치

    for (int i = 0; i < lenT; i++) {
      //	pattern의 idx번째 문자와 text의 i번째 문자가 불일치할 경우
      //	현재 pattern의 위치를 failure[idx - 1]로 옮겨서 다시 비교
      while (idx > 0 && text[i] != pattern[idx]) {
        idx = failure[idx - 1];
      }

      if (text[i] == pattern[idx]) {
        if (idx == lenP - 1) {    //	pattern을 찾은 경우
          resList.add(i - idx);
          idx = failure[idx];
        } else
          idx++;    //	다음 글자 비교해야 함
      }
    }

    return resList;
  }
} //    Main-class-end
```