# 26_03_25_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1445 일요일 아침의 데이트

[1445번: 일요일 아침의 데이트](http://boj.ma/1445/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
  private static final int INF = Integer.MAX_VALUE;
  private static final char START    = 'S';		//	시작점
  private static final char FLOWER   = 'F';		//	꽃
  private static final char GARBAGE  = 'g';		//	쓰레기
  private static final char ADJACENT = '*';		//	쓰레기 인접 칸 (S, F는 제외)

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken()); //	세로 크기
    int m = Integer.parseInt(st.nextToken()); //	가로 크기

    char[][] map = new char[n][m];
    //	dist[0][i][j] : (i, j)칸까지 오는데 만나는 쓰레기 칸 최소 개수
    //	dist[1][i][j] : (i, j)칸까지 오는데 만나는 쓰레기 인접 칸 최소 개수
    int[][][] dist = new int[2][n][m];

    int sy = -1;
    int sx = -1;  //	시작점 위치
    int fy = -1;
    int fx = -1;  //	꽃 위치

    for(int i = 0; i < 2; i++) {
      for(int j = 0; j < n; j++)
        Arrays.fill(dist[i][j], INF);
    }

    for(int i =  0; i < n; i++) {
      char[] input = br.readLine().toCharArray();

      for(int j = 0; j < m; j++) {
        if(!(input[j] == '.' && map[i][j] == '*'))
          map[i][j] = input[j];

        if(input[j] == GARBAGE) {
          for(int d = 0; d < 4; d++) {
            int ny = i + dy[d];
            int nx = j + dx[d];

            if(isIn(ny, nx, n, m) && map[ny][nx] != START && map[ny][nx] != FLOWER && map[ny][nx] != GARBAGE)
              map[ny][nx] = ADJACENT;
          }
        }
        else if(input[j] == START) {
          sy = i;
          sx = j;
        }
        else if(input[j] == FLOWER) {
          fy = i;
          fx = j;
        }
      }
    }	//	input-end

    dijkstra(n, m, sy, sx, map, dist);
    System.out.println(dist[0][fy][fx] + " " + dist[1][fy][fx]);
  } //	main-end

  private static void dijkstra(int n, int m, int sy, int sx, char[][] map, int[][][] dist) {
    PriorityQueue<Node> pq = new PriorityQueue<>();
    dist[0][sy][sx] = dist[1][sy][sx] = 0;	//	시작점은 쓰레기도, 쓰레기 인접칸도 아님.
    pq.offer(new Node(sy, sx, dist[0][sy][sx], dist[1][sy][sx]));

    while(!pq.isEmpty()) {
      Node cNode = pq.poll();
      int cy = cNode.y;
      int cx = cNode.x;
      int cGarbage = cNode.garbage;
      int cAdjacent = cNode.adjacent;

      if(!isPriorOrEqual(cNode, cy, cx, dist))	//	더 갱신되지 않을 경우
        continue;

      for(int d = 0; d < 4; d++) {
        int ny = cy + dy[d];
        int nx = cx + dx[d];
        int nGarbage = cGarbage;
        int nAdjacent = cAdjacent;
        Node nNode;

        if(!isIn(ny, nx, n, m))	//	다음 칸이 범위 벗어날 경우
          continue;

        if(map[ny][nx] == GARBAGE)
          nGarbage++;
        else if(map[ny][nx] == ADJACENT)
          nAdjacent++;

        nNode = new Node(ny, nx, nGarbage, nAdjacent);

        if(isPrior(nNode, ny, nx, dist)) {
          dist[0][ny][nx] = nNode.garbage;
          dist[1][ny][nx] = nNode.adjacent;
          pq.offer(nNode);
        }
      }	//	for-4-dir-end
    }	//	while-end
  }	//	dijkstra-end

  //	node로 접근해서 (y, x)를 갱신 가능하면 true
  private static boolean isPrior(Node node, int y, int x, int[][][] dist) {
    int garbage = node.garbage;
    int adjacent = node.adjacent;

    if(garbage < dist[0][y][x])
      return true;
    else if(garbage == dist[0][y][x])
      return adjacent < dist[1][y][x];

    return false;
  }

  //	node로 접근해서 (y, x)를 갱신 가능하거나, 같을 경우
  private static boolean isPriorOrEqual(Node node, int y, int x, int[][][] dist) {
    boolean res = isPrior(node, y, x, dist);

    if(!res)
      res = (node.garbage == dist[0][y][x] && node.adjacent == dist[1][y][x]);

    return res;
  }

  private static final int[] dy = {0, 1, 0, -1};
  private static final int[] dx = {1, 0, -1, 0};

  private static boolean isIn(int y, int x, int n, int m) {
    return (0 <= y && y < n) && (0 <= x && x < m);
  }

  private static class Node implements Comparable<Node> {
    public int y;
    public int x;			//	위치
    public int garbage;	    //	(y, x)까지 오며 지나온 쓰레기칸 개수
    public int adjacent;	//	(y, x)까지 오며 지나온 쓰레기와 인접한 칸  개수

    public Node(int y, int x, int garbage, int adjacent) {
      this.y = y;
      this.x = x;
      this.garbage = garbage;
      this.adjacent = adjacent;
    }

    @Override
    public int compareTo(Node other) {
      int res = Integer.compare(this.garbage, other.garbage);

      return res != 0 ? res : Integer.compare(this.adjacent, other.adjacent);
    }
  }
} //	Main-class-end
```