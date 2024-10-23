# 24_10_23_daily_certification

# Blog

### 벽 부수고 이동하기 4

[BOJ 16946 벽 부수고 이동하기 4 (Java)](https://koreii2425.tistory.com/entry/BOJ-16946-벽-부수고-이동하기-4-Java)

# Problem Solving (Algorithm & SQL)

### BOJ 16946 벽 부수고 이동하기 4

[](https://www.acmicpc.net/problem/16946)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Set;
import java.util.HashSet;
import java.util.Arrays;
import java.util.List;
import java.util.ArrayList;
import java.util.Queue;
import java.util.LinkedList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;
    private static int M;    //    맵 크기
    
    private static int[][] map;            //    0 : 빈칸, 1 ~ walls : 벽의 번호
    private static boolean[][] visited;    //    방문처리 배열
    
    private static int walls = 0;        //    벽 개수
    private static int[] wallCnts;       //    wallCnts[i] : i번째 벽에서 갈 수 있는 빈칸 개수 (i번째 벽 포함)
    
    private static final List<int[]> wallPosList = new ArrayList<>();    //    wallPosList[i] ; i번째 벽의 좌표
    
    private static char[][] answer;        //    정답 배열
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        wallPosList.add(new int[] {-1, -1});    //    dummy data
        
        map = new int[N][M];
        visited = new boolean[N][M];
        for(int i = 0; i < N; i++) {
            char[] input = br.readLine().toCharArray();
            for(int j = 0; j < M; j++) {
                char ch = input[j];
                
                if(ch == '1') {    //    (i, j)칸이 벽일 경우
                    walls++;    //    벽 개수 1 증가
                    map[i][j] = walls;    //    (i, j)칸을 벽으로 표시
                    wallPosList.add(new int[] {i, j});    //    walls번째 벽은 (i, j)에 위치함
                }
            }
        }
        
        wallCnts = new int[walls + 1];
        Arrays.fill(wallCnts, 1);    //    벽의 위치 포함, 벽에서 갈 수 있는 칸의 수는 최소 1 이상
        
        for(int i = 0; i < N; i++) {
            for(int j = 0; j < M; j++) {
                if(map[i][j] == 0 && !visited[i][j])    //    빈 칸이고 방문하지 않았을 경우
                    bfs(i, j);
            }
        }
        
        answer = new char[N][M];
        for(int i = 0; i < N; i++)
            Arrays.fill(answer[i], '0');
        
        for(int i = 1; i <= walls; i++) {
            int[] pos = wallPosList.get(i);    //    i번째 벽의 좌표
            int y = pos[0];
            int x = pos[1];
            answer[y][x] = (char)((wallCnts[i] % 10) + '0');
        }
            
        for(int i = 0; i < N; i++)
            sb.append(new String(answer[i])).append("\n");
        
        System.out.print(sb);
    }    //    main-end
    
    private static void bfs(int y, int x) {
        Queue<int[]> q = new LinkedList<>();
        Set<Integer> set = new HashSet<>();    //    빈칸 (y, x)가 포함된 빈칸의 connected component와 인접한 벽의 번호 집합
        int cnt = 1;    //    빈칸 (y, x)가 포함된 빈칸의 connected component의 크기
        
        visited[y][x] = true;
        q.offer(new int[] {y, x});
        
        while(!q.isEmpty()) {
            int[] cur = q.poll();
            int cy = cur[0];
            int cx = cur[1];    //    현재 벽 위치
            
            for(int d = 0; d < 4; d++) {    //    주변 4칸 탐색
                int ny = cy + dy[d];
                int nx = cx + dx[d];    //    주변 칸
                
                if(isIn(ny, nx)) {    //    범위 안일 경우
                    if(map[ny][nx] == 0 && !visited[ny][nx]) {    //    빈칸이고 방문하지 않았을 경우
                        cnt++;    //    connected component 크기 1 증가
                        visited[ny][nx] = true;    //    방문 처리
                        q.offer(new int[] {ny, nx});
                    }
                    else if(map[ny][nx] >= 1)    //    벽일 경우
                        set.add(map[ny][nx]);    //    인접한 벽 집합에 추가
                }
            }
        }
        
        for(int wall : set)
            wallCnts[wall] += cnt;
    }
    
    private static final int[] dy = {0, 1, 0, -1};
    private static final int[] dx = {1, 0, -1, 0};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < N) && (0 <= x && x < M);
    }
}    //    Main-class-end
```

### **CO{)E TREE 1이 k개 이상 존재하는 부분 수열**

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/subsequence-with-k-or-more-1s/description)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;

    private static int N;
    private static int K;

    private static int[] nums;

    private static int minLen = Integer.MAX_VALUE;

    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());

        nums = new int[N];
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++)
            nums[i] = Integer.parseInt(st.nextToken());

        int start = 0;
        int end = 0;
        int cnt = nums[end] == 1 ? 1 : 0;    //  [start, end]에 포함된 1의 개수
        int len = 1;

        if(cnt >= K)
            minLen = Math.min(minLen, len);

        while(true) {
            if(cnt < K) {   //  [start, end] 구간의 1의 개수가 부족할 경우, end를 키워서 1의 개수를 키워야 함
                if(end + 1 < N) {   //  다음 수가 존재해서 end를 키울 수 있을 경우
                    cnt += nums[end + 1] == 1 ? 1 : 0;
                    end++;
                }
                else break; //  더 늘릴 수 없으므로 종료
            }
            else {  //  1의 개수가 충분할 경우
                len = end - start + 1;  //  [start, end] 구간 길이
                minLen = Math.min(minLen, len);

                cnt -= nums[start] == 1 ? 1 : 0;    //  start를 키워서 구간 길이 줄이기
                start++;
            }
        }

        System.out.println(minLen == Integer.MAX_VALUE ? -1 : minLen);
    }   //  main-end
}   //  Main-class-end
```