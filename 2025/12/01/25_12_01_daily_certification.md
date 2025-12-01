# 25_12_01_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2021 최소 환승 경로

[2021번: 최소 환승 경로](http://boj.ma/2021/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Queue;
import java.util.ArrayDeque;

public class Main {
    private static final int INF = Integer.MAX_VALUE;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    역 개수
    private static int L;    //    노선 개수
    
    private static List<Integer>[] stationToLine;
    private static List<Integer>[] lineToStation;
    
    private static int SRC;               //    출발역
    private static int DST;               //    도착역
    private static boolean[] checked;     //    checked[l] : l번 노선 체크했으면 true
    private static int[] dist;            //    dist[s] : s번 역에 닿기 위한 최소 환승 개수
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        L = Integer.parseInt(st.nextToken());
        
        stationToLine = new ArrayList[N + 1];
        lineToStation = new ArrayList[L + 1];
        dist = new int[N + 1];
        checked = new boolean[L + 1];
        for(int station = 1; station <= N; station++) {
            stationToLine[station] = new ArrayList<>();
            dist[station] = INF;
        }
        for(int line = 1; line <= L; line++) {
            lineToStation[line] = new ArrayList<>();
            
            st = new StringTokenizer(br.readLine());
            while(st.hasMoreTokens()) {
                int station = Integer.parseInt(st.nextToken());
                if(station != -1) {
                    stationToLine[station].add(line);
                    lineToStation[line].add(station);
                }
            }
        }
        
        st = new StringTokenizer(br.readLine());
        SRC = Integer.parseInt(st.nextToken());
        DST = Integer.parseInt(st.nextToken());
        
        System.out.println(bfs());
    }    //    main-end
    
    private static int bfs() {
        Queue<int[]> queue = new ArrayDeque<>();
        int time = 0;

        dist[SRC] = 0;
        queue.offer(new int[] {SRC, dist[SRC]});
        
        while(!queue.isEmpty()) {
            int size = queue.size();
            
            for(int i = 0; i < size; i++) {
                int[] cur = queue.poll();
                int curStation = cur[0];
                int curStationDist = cur[1];
            
                for(int curLine : stationToLine[curStation]) {
                    if(!checked[curLine]) {    //    아직 체크 안한 호선의 경우
                        checked[curLine] = true;
                    
                        for(int nextStation : lineToStation[curLine]) {    //    현재 호선에서 갈 수 있는 역들
                            if(time < dist[nextStation]) {
                                dist[nextStation] = time;
                                queue.offer(new int[] {nextStation, dist[nextStation]});
                            }
                        }
                    }
                }
            }
            
            time++;
        }
        
        return dist[DST] == INF ? -1 : dist[DST];
    } 
}    //    Main-class-end
```