# 26_02_08_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16986 인싸들의 가위바위보

[16986번: 인싸들의 가위바위보](http://boj.ma/16986/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
  private static final int W = 2;
  private static final int D = 1;

  private static boolean find = false;    //	0번째 사람이 이긴 경우를 찾았을 경우 true

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken());       //	손동작 수
    int k = Integer.parseInt(st.nextToken());       //	필요한 승수

    int[][] comparative = new int[n + 1][n + 1];    //	손동작 별로 승/무/패
    int[][] rsp = new int[3][20];                   //	rsp[i][j] : i번째 사람이 본인이 참여하는 j번째 경기에 낼 손동작
    boolean[] used = new boolean[10];

    for (int i = 1; i <= n; i++) {
      st = new StringTokenizer(br.readLine());
      for (int j = 1; j <= n; j++)
        comparative[i][j] = Integer.parseInt(st.nextToken());
    }

    for (int i = 1; i <= 2; i++) {
      st = new StringTokenizer(br.readLine());
      for (int j = 0; j < 20; j++)
        rsp[i][j] = Integer.parseInt(st.nextToken());
    }

    for (int i = 1; i <= n; i++) {
      Arrays.fill(used, false);

      used[i] = true;
      rsp[0][0] = i;
      dfs(0, 1, 2, 0, 0, 0, 0, 0, 0, n, k, comparative, rsp, used);

      if (find) {
        System.out.println(1);
        return;
      }
    }

    System.out.println(0);
  }    //    main-end

  //	player1 : 이번 경기를 할 첫 번쨰 플레이어
  //	player2 : 이번 경기를 할 두 번째 플레이어
  //	nextPlayer : 이번 경기의 승자와 다음 경기를 할 플레이어
  //	player1Idx : player1이 참여하는 경기 횟수
  //	player2Idx : player2가 참여하는 경기 횟수
  //	nextPlayerIdx : nextPlayer 참여하는 경기 횟수
  //	player1Cnt : player1의 승리 횟수
  //	player2Cnt : player2가 승리 횟수
  //	nextPlayerCnt : nextPlayer의 승리 횟수
  private static void dfs(int player1, int player2, int nextPlayer, int player1Idx, int player2Idx, int nextPlayerIdx, int player1Cnt, int player2Cnt, int nextPlayerCnt, int n, int k, int[][] comparative, int[][] rsp, boolean[] used) {
    int p1Rsp = rsp[player1][player1Idx];
    int p2Rsp = rsp[player2][player2Idx];    //	각각 player1, player2가 내놓는 손동작

    if (comparative[p1Rsp][p2Rsp] == W || (comparative[p1Rsp][p2Rsp] == D && player1 > player2)) {    //	player1이 이길 경우, 다음 게임은 player1과 nextPlayer가 진행
      if (player1Cnt + 1 == k) {    //	player1이 K번 승리하여 우승한 경우, 게임이 끝남
        if (player1 == 0)    //	주인공이 이긴 경우
          find = true;    //	주인공이 이기는 경우 찾음
        return;    //	게임 끝남
      }

      if (player1 == 0 || nextPlayer == 0) {    //	다음 게임하는 플레이어 중에 주인공 존재할 경우, 주인공이 다음에 낼 손동작 정해야 함
        for (int i = 1; i <= n; i++) {
          if (!used[i]) {    //	주인공이 i번째 손동작을 아직 안썼을 경우
            used[i] = true;
            rsp[0][player1 == 0 ? player1Idx + 1 : nextPlayerIdx] = i;
            dfs(player1, nextPlayer, player2, player1Idx + 1, nextPlayerIdx, player2Idx + 1, player1Cnt + 1, nextPlayerCnt, player2Cnt, n, k, comparative, rsp, used);
            used[i] = false;
          }
        }
      } else
        dfs(player1, nextPlayer, player2, player1Idx + 1, nextPlayerIdx, player2Idx + 1, player1Cnt + 1, nextPlayerCnt, player2Cnt, n, k, comparative, rsp, used);
    } else {    //	player2가 이길 경우, 다음 게임은 player2와 nextPlayer가 진행
      if (player2Cnt + 1 == k) {    //	player2가 K번 승리하여 우승한 경우, 게임이 끝남
        if (player2 == 0)    //	주인공이 이긴 경우
          find = true;    //	주인공이 이기는 경우 찾음
        return;    //	게임 끝남
      }

      if (player2 == 0 || nextPlayer == 0) {    //	다음 게임하는 플레이어 중에 주인공 존재할 경우, 주인공이 다음에 낼 손동작 정해야 함
        for (int i = 1; i <= n; i++) {
          if (!used[i]) {    //	주인공이 i번째 손동작을 아직 안썼을 경우
            used[i] = true;
            rsp[0][player2 == 0 ? player2Idx + 1 : nextPlayerIdx] = i;
            dfs(player2, nextPlayer, player1, player2Idx + 1, nextPlayerIdx, player1Idx + 1, player2Cnt + 1, nextPlayerCnt, player1Cnt, n, k, comparative, rsp, used);
            used[i] = false;
          }
        }
      } else
        dfs(player2, nextPlayer, player1, player2Idx + 1, nextPlayerIdx, player1Idx + 1, player2Cnt + 1, nextPlayerCnt, player1Cnt, n, k, comparative, rsp, used);
    }
  }
}    //    Main-class-end
```