# 25_12_07_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 3954 Brainf**k 인터프리터

[3954번: Brainf**k 인터프리터](http://boj.ma/3954/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Arrays;
import java.util.Deque;
import java.util.StringTokenizer;

public class Main {
  private static final int MAX_OPR = 50_000_000;  //  최대 명령어 수행 횟수
  private static final int MAX_VALUE = 1 << 8;

  private static int pointer = 0;  //  포인터 (메모리 가리키는 위치) (0 <= pointer < sm)
  private static int codePointer = 0;  //  코드 위치
  private static int inputPointer = 0; //  입력 위치
  private static int lastLoop = -1;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();

    int t = Integer.parseInt(br.readLine());  //  테스트케이스 개수

    for (int tc = 1; tc <= t; tc++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      int sm = Integer.parseInt(st.nextToken()); //  메모리 크기
      int sc = Integer.parseInt(st.nextToken()); //  코드 크기
      int si = Integer.parseInt(st.nextToken()); //  입력 크기

      pointer = 0;  //  포인터 (메모리 가리키는 위치) (0 <= pointer < sm)
      char[] memory = new char[sm]; //  메모리
      char[] code = br.readLine().toCharArray();
      char[] input = br.readLine().toCharArray();
      Deque<Integer> stack = new ArrayDeque<>();    //  루프 판별하기 위한 스택

      codePointer = 0;  //  코드 위치
      //  parenthesisMap[idx]
      //  code[idx]가 '['일 경우, 대응하는 ']'의 위치
      //  code[idx]가 ']'일 경우, 대응하는 '['의 위치
      int[] parenthesisMap = new int[sc];

      inputPointer = 0; //  입력 위치

      lastLoop = -1;

      Arrays.fill(parenthesisMap, -1);

      for (int i = 0; i < sc; i++) {
        if (code[i] == '[')
          stack.offerLast(i);
        else if (code[i] == ']') {
          int leftIdx = stack.pollLast();
          parenthesisMap[leftIdx] = i;
          parenthesisMap[i] = leftIdx;
        }
      }

      //  코드가 끝나지 않았고, 명령어 실행 횟수가 MAX_OPR 이하일 경우
      for(int i = 0; i < MAX_OPR; i++) {
        operate(memory, code, input, sm, si, parenthesisMap);
        if(codePointer == sc)
          break;
      }

      if (codePointer == sc) //  코드가 끝난 경우
        sb.append("Terminates\n");
      else { //  무한 루프 발생한 경우
        int openParenthesis = codePointer;
        int closeParenthesis = codePointer;

        for(int i = 0; i < MAX_OPR; i++) {
          operate(memory, code, input, sm, si, parenthesisMap);
          openParenthesis = Math.min(openParenthesis, codePointer);
          closeParenthesis = Math.max(closeParenthesis, codePointer);
        }

        sb.append("Loops ").append(openParenthesis - 1).append(" ").append(closeParenthesis).append("\n");
      }
    }

    System.out.print(sb);
  } //	main-end

  private static void operate(char[] memory, char[] code, char[] input, int sm, int si, int[] parenthesisMap) {
    char opr = code[codePointer]; //  수행할 명령어

    switch (opr) {
      case '-':
        memory[pointer] = (char) ((memory[pointer] - 1 + MAX_VALUE) % MAX_VALUE);
        codePointer++;
        break;
      case '+':
        memory[pointer] = (char) ((memory[pointer] + 1) % MAX_VALUE);
        codePointer++;
        break;
      case '<':
        pointer = (pointer - 1 + sm) % sm;
        codePointer++;
        break;
      case '>':
        pointer = (pointer + 1) % sm;
        codePointer++;
        break;
      case '[':
        if (memory[pointer] == (char) 0)  //  루프 탈출
          codePointer = parenthesisMap[codePointer] + 1;
        else
          codePointer++;
        break;
      case ']':
        if (memory[pointer] != (char) 0) {//  루프 반복
          if(lastLoop < codePointer)
            lastLoop = codePointer;

          codePointer = parenthesisMap[codePointer] + 1;
        }
        else  //  루프 탈출
          codePointer++;
        break;
      case '.':
        //  출력 (이 문제에서는 아무 일도 하지 않음)
        codePointer++;
        break;
      case ',':
        if (inputPointer == si)
          memory[pointer] = MAX_VALUE - 1;
        else
          memory[pointer] = input[inputPointer++];
        codePointer++;
    }
  }
} //	Main-class-end
```