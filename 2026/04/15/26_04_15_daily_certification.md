# 26_04_15_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 4574 스도미노쿠

[](http://boj.ma/4574/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static final StringBuilder sb = new StringBuilder();

  private static int TC = 1;	//	테스트 케이스 번호
  private static int N = 0;	//	미리 놓인 도미노 개수
  private static final char[][] sudoku = new char[9][9];
  private static final boolean[][] used = new boolean[10][10];		//	used[i][j] : i, j로 이루어진 도미노가 사용되었으면 true
  private static final List<int[]> dominos = new ArrayList<>();	//	남은 도미노 목록

  private static boolean find = false;	//	정답 찾으면 true

  public static void main(String[] args) throws IOException {
    while(true) {
      N = Integer.parseInt(br.readLine());

      if(N == 0) {
        System.out.print(sb);
        break;
      }

      init();
      input();
      dfs(0, 0);
    }
  }	//	main-end

  private static void dfs(int nth, int pos) {
    if(find || pos >= 81)	//	이미 정답을 찾았거나, 모든 칸을 다 탐색했을 경우
      return;

    if(nth == dominos.size()) {	//	남은 도미노를 모두 놓았을 경우
      find = true;	//	더 이상 탐색 필요 없음
      printAns();
      return;
    }

    int y = pos / 9;
    int x = pos % 9;	//	도미노 놓을 위치

    if(sudoku[y][x] != '0') {	//	현재 위치에 이미 도미노 놓여 있을 경우
      dfs(nth, pos + 1);
      return;
    }

    for(int i = 0; i < dominos.size(); i++) {
      int[] domino = dominos.get(i);
      int num1 = domino[0];
      int num2 = domino[1];

      if(used[num1][num2])	//	이미 사용된 도미노일 경우 skip
        continue;

      for(int d = 0; d < 2; d++) {
        int ny = y + dy[d];
        int nx = x + dx[d];	//	 (y, x)와 더불어 도미노가 놓일 칸

        if(!isIn(ny, nx) || sudoku[ny][nx] != '0')	//	(ny, nx)가 범위 밖이거나, 이미 다른 수가 있으면 skip
          continue;

        if(find)
          return;

        //	(y, x)에 num1, (ny, nx)에 num2 놓을 수 있으면
        if(canPutNum(num1, y, x) && canPutNum(num2, ny, nx)) {
          sudoku[y][x] = (char)('0' + num1);
          sudoku[ny][nx] = (char)('0' + num2);
          used[num1][num2] = true;	//	이 도미노 사용

          dfs(nth + 1, pos + 1);		//	다음 칸 탐색

          used[num1][num2] = false;
          sudoku[y][x] = '0';
          sudoku[ny][nx] = '0';	//	원상복구
        }

        if(find)
          return;

        //	(y, x)에 num2, (ny, nx)에 num1 놓을 수 있으면
        if(canPutNum(num2, y, x) && canPutNum(num1, ny, nx)) {
          sudoku[y][x] = (char)('0' + num2);
          sudoku[ny][nx] = (char)('0' + num1);
          used[num1][num2] = true;	//	이 도미노 사용

          dfs(nth + 1, pos + 1);		//	다음 칸 탐색

          used[num1][num2] = false;
          sudoku[y][x] = '0';
          sudoku[ny][nx] = '0';	//	원상복구
        }
      }
    }
  }

  private static int[] dy = {0, 1};
  private static int[] dx = {1, 0};

  private static boolean isIn(int y, int x) {
    return (0 <= y && y < 9) && (0 <= x && x < 9);
  }

  //	(y, x)에 num을 놓을 수 있으면 true, 놓을 수 없으면 false
  private static boolean canPutNum(int num, int y, int x) {
    char digit = (char)(num + '0');

    //	(y, x)가 포함된 3 * 3 정사각형 경계 
    int sy = (0 <= y && y < 3) ? 0 : ((3 <= y && y < 6) ? 3 : 6);
    int sx = (0 <= x && x < 3) ? 0 : ((3 <= x && x < 6) ? 3 : 6);
    int ey = (0 <= y && y < 3) ? 2 : ((3 <= y && y < 6) ? 5 : 8);
    int ex = (0 <= x && x < 3) ? 2 : ((3 <= x && x < 6) ? 5 : 8);

    //	3 * 3 정사각형 검사
    for(int row = sy; row <= ey; row++) {
      for(int col = sx; col <= ex; col++) {
        if(sudoku[row][col] == digit && (row != y || col != x))
          return false;
      }
    }

    //	세로선 검사
    for(int row = 0; row < 9; row++) {
      if(row != y && sudoku[row][x] == digit)
        return false;
    }

    //	가로선 검사
    for(int col = 0; col < 9; col++) {
      if(col != x && sudoku[y][col] == digit)
        return false;
    }

    return true;
  }

  private static void init() {
    for(int i = 0; i < 9; i++) {
      Arrays.fill(sudoku[i], '0');
      Arrays.fill(used[i], false);
    }

    find = false;
    dominos.clear();
  }

  private static void input() throws IOException {
    //	미리 주어진 N개 도미노 위치 받음
    StringTokenizer st;
    for(int i = 0; i < N; i++) {
      st = new StringTokenizer(br.readLine());
      char num1 = st.nextToken().charAt(0);
      String pos1 = st.nextToken();
      int y1 = pos1.charAt(0) - 'A';
      int x1 = pos1.charAt(1) - '1';

      sudoku[y1][x1] = num1;

      char num2 = st.nextToken().charAt(0);
      String pos2 = st.nextToken();
      int y2 = pos2.charAt(0) - 'A';
      int x2 = pos2.charAt(1) - '1';

      sudoku[y2][x2] = num2;

      used[num1 - '0'][num2 - '0'] = true;
      used[num2 - '0'][num1 - '0'] = true;
    }

    for(int num1 = 1; num1 <= 8; num1++) {
      for(int num2 = num1 + 1; num2 <= 9; num2++) {
        if(!used[num1][num2])	//	아직 사용되지 않은 도미노일 경우
          dominos.add(new int[] {num1, num2});
      }
    }

    st = new StringTokenizer(br.readLine());
    for(int num = 1; num <= 9; num++) {
      String pos = st.nextToken();
      int y = pos.charAt(0) - 'A';
      int x = pos.charAt(1) - '1';

      sudoku[y][x] = (char)('0' + num);
    }
  }

  private static void printAns() {
    sb.append("Puzzle ").append(TC++).append("\n");

    for(int i = 0; i < 9; i++) {
      for(int j = 0; j < 9; j++)
        sb.append(sudoku[i][j]);
      sb.append("\n");
    }
  }
}	//	Main-class-end
```