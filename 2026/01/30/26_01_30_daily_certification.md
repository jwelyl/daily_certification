# 26_01_29_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 16985 Maaaaaaaaaze**

[16985번: Maaaaaaaaaze](http://boj.ma/16985/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Arrays;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
  private static final int[][][][] map = new int[5][4][5][5];
  private static final int[][][] maze = new int[5][5][5];
  private static final boolean[][][] visited = new boolean[5][5][5];

  private static int sz;
  private static int sy;
  private static int sx;
  private static int ez;
  private static int ey;
  private static int ex;

  private static final int[] floorOrder = new int[5];
  private static final int[] rotState = new int[5];

  private static int minTime = Integer.MAX_VALUE;

  public static void main(String[] args) throws IOException {
    BufferedReader bf = new BufferedReader(new InputStreamReader(System.in));
    for (int floor = 0; floor < 5; floor++) {
      for (int i = 0; i < 5; i++) {
        StringTokenizer tokens = new StringTokenizer(bf.readLine());
        for (int j = 0; j < 5; j++)
          map[floor][0][i][j] = Integer.parseInt(tokens.nextToken());
      }
    }

    for (int floor = 0; floor < 5; floor++) {
      for (int r = 0; r < 3; r++)
        rotate90(map[floor][r], map[floor][r + 1]);
    }

    setFloors(0, new boolean[5]);

    System.out.println(minTime == Integer.MAX_VALUE ? -1 : minTime);
  }

  private static void rotate90(int[][] before, int[][] after) {
    for (int h = 0; h < 5; h++) {
      for (int w = 0; w < 5; w++)
        after[w][4 - h] = before[h][w];
    }
  }

  private static void setFloors(int nth, boolean[] isUsed) {
    if (nth == 5) {
      setRotations(0);
      return;
    }

    for (int i = 0; i < 5; i++) {
      if (!isUsed[i]) {
        isUsed[i] = true;
        floorOrder[nth] = i;
        setFloors(nth + 1, isUsed);
        isUsed[i] = false;
      }
    }
  }

  private static void setRotations(int nth) {
    if (nth == 5) {
      makeMaze();

      sz = 0; sy = 0; sx = 0;
      ez = 4; ey = 4; ex = 4;
      if(maze[sz][sy][sx] == 1 && maze[ez][ey][ex] == 1)
        bfs();

      return;
    }

    for (int i = 0; i < 4; i++) {
      rotState[nth] = i;
      setRotations(nth + 1);
    }
  }

  private static void resetVisited() {
    for(int floor = 0; floor < 5; floor++) {
      for(int h = 0; h < 5; h++)
        Arrays.fill(visited[floor][h], false);
    }
  }

  private static void makeMaze() {
    for (int floor = 0; floor < 5; floor++) {
      int f = floorOrder[floor];
      int s = rotState[floor];

      for(int h = 0; h < 5; h++)
        System.arraycopy(map[f][s][h], 0, maze[floor][h], 0, 5);
    }
  }

  private static final int[] dz = {-1, 0, 0, 0, 0, 1};
  private static final int[] dy = {0, 0, 1, 0, -1, 0};
  private static final int[] dx = {0, 1, 0, -1, 0, 0};

  private static boolean isIn(int z, int y, int x) {
    return (0 <= z && z < 5) && (0 <= y && y < 5) && (0 <= x && x < 5);
  }

  private static void bfs() {
    Queue<Node> queue = new ArrayDeque<>();
    int time = 0;

    resetVisited();

    visited[sz][sy][sx] = true;
    queue.offer(new Node(sz, sy, sx));

    while(!queue.isEmpty()) {
      int size = queue.size();
      time++;

      for(int s = 0; s < size; s++) {
        Node node = queue.poll();
        int cz = node.z;
        int cy = node.y;
        int cx = node.x;

        for(int d = 0; d < 6; d++) {
          int nz = cz + dz[d];
          int ny = cy + dy[d];
          int nx = cx + dx[d];

          if(!isIn(nz, ny, nx) || maze[nz][ny][nx] == 0 || visited[nz][ny][nx])
            continue;

          if(nz == ez && ny == ey && nx == ex) {
            minTime = Math.min(minTime, time);
            return;
          }

          visited[nz][ny][nx] = true;
          queue.offer(new Node(nz, ny, nx));
        }
      }
    }
  }

  private static class Node {
    public int z;
    public int y;
    public int x;

    public Node(int z, int y, int x) {
      this.z = z;
      this.y = y;
      this.x = x;
    }
  }
} //  Main-class-end
```