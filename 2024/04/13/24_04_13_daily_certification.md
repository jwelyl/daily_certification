# 24_04_13_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 기울어진 직사각형**

[https://www.codetree.ai/missions/2/problems/slanted-rectangle/description](https://www.codetree.ai/missions/2/problems/slanted-rectangle/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/2/problems/slanted-rectangle/description)

**BOJ 1451 직사각형으로 나누기**

[1451번: 직사각형으로 나누기](https://www.acmicpc.net/problem/1451)

1, 2, 3, 4 방향으로 적어도 한 번씩 이동해서 기울어진 직사각형을 만들어야 한다.

기울어진 직사각형이 격자 범위를 벗어나면 안되므로 시작점, 1, 3 방향 이동 가능 거리 k1, 2, 4 방향 이동 가능 거리 k2에 제약이 존재하지만 이를 고려하고 코드를 작성할 필요는 없다.

어차피 격자 크기 n은 최대 20이고 k1, k2도 n 이하이므로 4중 for문으로 시작점 (y, x)와 k1, k2를 정할 수 있다. (y, x)와 k1, k2를 정한 후 기울어진 직사각형의 각 꼭짓점이 범위를 벗어나는지 체크 후 벗어나지 않는다면 추가로 O(k1 + k2)로 직사각형에 속한 수의 합을 구할 수 있다. 따라서 O(n^5)로 해결할 수 있다.

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;       //  격자 크기
    private static int[][] map; //  격자

    private static int ans = 0; //  기울어진 직사각형에 포함된 숫자 합 최댓갑

    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());

        map = new int[n][n];

        for(int i = 0; i < n; i++) {
            tokens = new StringTokenizer(br.readLine());
            for(int j = 0; j < n; j++)
                map[i][j] = Integer.parseInt(tokens.nextToken());
        }

        for(int y = 0; y < n; y++) {
            for(int x = 0; x < n; x++) {
                //  시작점을 (y, x)로 함
                for(int k1 = 1; k1 <= n; k1++) {
                    for(int k2 = 1; k2 <= n; k2++) {
                        //  1, 3 방향으로 대각선으로 k1 이동
                        //  2, 4 방향으로 대각선으로 k2 이동
                        int sum = 0;

                        int py1 = y + dy[0] * k1;
                        int px1 = x + dx[0] * k1;   //  (y, x)에서 1 방향으로 k1만큼 이동한 좌표

                        if(!isIn(py1, px1)) //  (y, x)에서 1 방향으로 k1만큼 이동할 수 없을 경우
                            continue;

                        for(int k = 1; k <= k1; k++) {
                            sum += map[y + dy[0] * k][x + dx[0] * k];
                        }

                        int py2 = py1 + dy[1] * k2;
                        int px2 = px1 + dx[1] * k2; //  (py1, px1)에서 2 방향으로 k2만큼 이동한 좌표

                        if(!isIn(py2, px2)) //  (py1, px1)에서 2 방향으로 k2만큼 이동할 수 없을 경우
                            continue;        
                        
                        for(int k = 1; k <= k2; k++) {
                            sum += map[py1 + dy[1] * k][px1 + dx[1] * k];
                        }

                        int py3 = py2 + dy[2] * k1;
                        int px3 = px2 + dx[2] * k1; //  (py2, px2)에서 3 방향으로 k1만큼 이동한 좌표

                        if(!isIn(py3, px3)) //  (py2, px2)에서 3 방향으로 k1만큼 이동할 수 없을 경우
                            continue;

                        for(int k = 1; k <= k1; k++) {
                            sum += map[py2 + dy[2] * k][px2 + dx[2] * k];
                        }

                        for(int k = 1; k <= k2; k++) {
                            sum += map[py3 + dy[3] * k][px3 + dx[3] * k];
                        }

                        ans = Math.max(ans, sum);
                    }
                }
            }
        }

        System.out.println(ans);
    }   //  main-end

    private static final int[] dy = {-1, -1, 1, 1};
    private static final int[] dx = {1, -1, -1, 1};

    private static boolean isIn(int y, int x) {
        return (0 <= y && y < n) && (0 <= x && x < n);
    }
}   //  Main-class-end
```