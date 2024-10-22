# 24_10_22_daily_certification

# Blog

### equals, hashCode

[equals와 hashCode를 함께 오버라이딩해야 하는 이유](https://koreii2425.tistory.com/entry/equals와-hashCode를-함께-오버라이딩해야-하는-이유)

# Problem Solving (Algorithm & SQL)

### BOJ 2206 벽 부수고 이동하기

[](https://www.acmicpc.net/problem/2206)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.LinkedList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    
    private static char[][] map;
    private static boolean[][][] visited;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        map = new char[N][M];
        for(int y = 0; y < N; y++)
            map[y] = br.readLine().toCharArray();
        
        visited = new boolean[N][M][2];
        
        System.out.println(bfs());
    }    //    main-end
    
    private static int bfs() {
        if(N == 1 && M == 1)
            return 1;
        
        //    {y, x, 부술 수 있는 벽의 개수, 이동 횟수}
        Queue<int[]> q = new LinkedList<>();
        visited[0][0][1] = true;
        q.offer(new int[] {0, 0, 1, 1});
        
        while(!q.isEmpty()) {
            int[] cur = q.poll();
            int cy = cur[0];
            int cx = cur[1];         //    현재 위치
            int cbreak = cur[2];     //    부술 수 있는 벽의 개수    
            int ccnt = cur[3];       //    현재 위치까지 이동한 횟수
            
            for(int d = 0; d < 4; d++) {
                int ny = cy + dy[d];
                int nx = cx + dx[d];    //    다음 위치
                
                if(!isIn(ny, nx))    //    범위를 벗어날 경우
                    continue;
                
                if(ny == N - 1 && nx == M - 1)    //    다음 위치가 도착점일 경우 (도착점은 벽이 아님)
                    return ccnt + 1;
                
                if(map[ny][nx] == '1') {    //    다음 위치가 벽일 경우
                    if(cbreak == 1) {   //    아직 벽을 부술 수 있는 경우
                        if(!visited[ny][nx][0]) {    //    벽을 부숴서 방문한 적이 없을 경우
                            visited[ny][nx][0] = true;
                            q.offer(new int[] {ny, nx, 0, ccnt + 1});
                        }
                    }
                }
                else {    //    다음 위치가 벽이 아닐 경우
                    if(!visited[ny][nx][cbreak]) {    //    현재 상태로 방문한 적이 없는 경우
                        visited[ny][nx][cbreak] = true;
                        q.offer(new int[] {ny, nx, cbreak, ccnt + 1});
                    }
                }
            }
        }    //    while-end
        
        return -1;    //    도달할  수 없을 경우
    }    //    bfs-end
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < N) && (0 <= x && x < M);
    }
}    //    Main-class-end
```

### BOJ 14442 벽 부수고 이동하기 2

[](https://www.acmicpc.net/problem/14442)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.LinkedList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    private static int K;
    
    private static char[][] map;
    private static boolean[][][] visited;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        map = new char[N][M];
        for(int y = 0; y < N; y++)
            map[y] = br.readLine().toCharArray();
        
        visited = new boolean[N][M][K + 1];
        
        System.out.println(bfs());
    }    //    main-end
    
    private static int bfs() {
        if(N == 1 && M == 1)
            return 1;
        
        //    {y, x, 부술 수 있는 벽의 개수, 이동 횟수}
        Queue<int[]> q = new LinkedList<>();
        visited[0][0][K] = true;
        q.offer(new int[] {0, 0, K, 1});
        
        while(!q.isEmpty()) {
            int[] cur = q.poll();
            int cy = cur[0];
            int cx = cur[1];         //    현재 위치
            int cbreak = cur[2];     //    부술 수 있는 벽의 개수    
            int ccnt = cur[3];       //    현재 위치까지 이동한 횟수
            
            for(int d = 0; d < 4; d++) {
                int ny = cy + dy[d];
                int nx = cx + dx[d];    //    다음 위치
                
                if(!isIn(ny, nx))    //    범위를 벗어날 경우
                    continue;
                
                if(ny == N - 1 && nx == M - 1)    //    다음 위치가 도착점일 경우 (도착점은 벽이 아님)
                    return ccnt + 1;
                
                if(map[ny][nx] == '1') {    //    다음 위치가 벽일 경우
                    if(cbreak > 0) {   //    아직 벽을 부술 수 있는 경우
                        if(!visited[ny][nx][cbreak - 1]) {    //    벽을 부숴서 방문한 적이 없을 경우
                            visited[ny][nx][cbreak - 1] = true;
                            q.offer(new int[] {ny, nx, cbreak - 1, ccnt + 1});
                        }
                    }
                }
                else {    //    다음 위치가 벽이 아닐 경우
                    if(!visited[ny][nx][cbreak]) {    //    현재 상태로 방문한 적이 없는 경우
                        visited[ny][nx][cbreak] = true;
                        q.offer(new int[] {ny, nx, cbreak, ccnt + 1});
                    }
                }
            }
        }    //    while-end
        
        return -1;    //    도달할  수 없을 경우
    }    //    bfs-end
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < N) && (0 <= x && x < M);
    }
}    //    Main-class-end
```

### **CO{)E TREE 폭탄 해체 작업**

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/the-bomb-dismantling/description)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;

    private static int N;   //  폭탄 수

    private static final List<Bomb> bombs = new ArrayList<>();
    
    //	폭탄 하나 해체하는데 1의 시간이 걸리므로 pq의 원소 개수는 pq 안에 있는 폭탄을 모두 해체하는데 걸리는 총 시간임
    private static final PriorityQueue<Integer> pq = new PriorityQueue<>();

    private static int ans = 0; //  얻을 수 있는 최대 보상

    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());

        for(int i = 0; i < N; i++) {
            st = new StringTokenizer(br.readLine());
            bombs.add(new Bomb(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken())));
        }

        Collections.sort(bombs);    //  데드라인 작은 순서대로 정렬

        for(Bomb bomb : bombs) {    //  데드라인이 작은 폭탄부터
            int reward = bomb.reward;
            int deadline = bomb.deadline;

            pq.offer(reward);	//	현재 폭탄을 해체했을때의 보상을 pq에 삽입
            
            if(deadline < pq.size())	//	현재 폭탄 해체 데드라인보다 많은 폭탄이 있을 경우
            	pq.poll();	//	해체했을때 보상이 가장 작은 폭탄의 해체를 포기
        }
        
        //	해체할 수 있는 폭탄들 해체 보상 더하
        while(!pq.isEmpty())
        	ans += pq.poll();
        
        System.out.println(ans);
    }   //  main-end

    private static class Bomb implements Comparable<Bomb> {
        public int reward;      //  해체했을때 보상
        public int deadline;    //  해체 데드라인

        public Bomb(int reward, int deadline) {
            this.reward = reward;
            this.deadline= deadline;
        }

        @Override
        public int compareTo(Bomb bomb) {
            // 해체 데드라인이 작은 폭탄 순으로 정렬
            return Integer.compare(this.deadline, bomb.deadline);
        }
    }
}   //  Main-class-end
```