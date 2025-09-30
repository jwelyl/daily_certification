# 25_10_01_daily_certification

# To Do List

```
[#274 koreii] 데일리인증 20251001
0. 역량검사
- 면접 질문 정리
1. 서류 작성
- 초안 작성 완료
- 중간 첨삭, 피드백
- 추가 공고 정리
2. 알고리즘 문제 풀이
- BOJ 15459 Haybale Feast (Tree Map, Two Pointer)
- BOJ 12869 뮤탈리스크 (BFS, DP)
```

```
[#93] 데일리인증 20251001 (274 / 365)
0. 8시간 채우기 휴식 (0/8)
1. 취업
- 면접 응시, 면접 복기
- 서류 2 제출, 추석 이후 마감 공고 처리 계획
2. 알고리즘 문제 풀이
- BOJ 15459 Haybale Feast (Tree Map, Two Pointer)
- BOJ 12869 뮤탈리스크 (BFS, DP)
```

# Problem Solving (Algorithm & SQL)

### BOJ 15459 **Haybale Feast**

[15459번: Haybale Feast](http://boj.ma/15459/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.TreeMap;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;        //    건초 개수
    private static long M;       //    식사를 구성하기 위해 필요한 건초 맛의 합의 최소
    
    private static int[][] haybales; 
    
    private static int start = 0;
    private static int end = 0;
    //    [start, end] 구간의 건초의 맛의 합
    private static long sum = 0;
    //    Key : [start, end] 구간에 포함된 매운 맛, Value : 그 매운 맛 개수
    private static final TreeMap<Integer, Integer> map = new TreeMap<>();
    
    //    식사를 구성했을 때 식사에 포함된 매운 맛의 최댓값의 최소
    private static int answer = Integer.MAX_VALUE;

    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Long.parseLong(st.nextToken());
        
        haybales = new int[N][2];
        for(int i = 0; i < N; i++) {
            st = new StringTokenizer(br.readLine());
            haybales[i][0] = Integer.parseInt(st.nextToken());    //    맛
            haybales[i][1] = Integer.parseInt(st.nextToken());    //    매운 맛
        }
        
        sum = haybales[0][0];
        map.put(haybales[0][1], 1);
        
        while(end < N) {
            if(sum >= M) {    //    맛의 합이 M 이상이라 식사 구성 가능할 경우
                answer = Math.min(answer, map.lastKey());    //    매운맛 최댓값의 최소 갱신
                
                //    start를 1 증가시켜 매운맛 최댓값 줄여보기
                int flavor = haybales[start][0];
                int spicy = haybales[start][1];
                int cnt = map.get(spicy);
                sum -= flavor;
                
                if(cnt == 1)
                    map.remove(spicy);
                else
                    map.put(spicy, cnt - 1);
                
                if(start < end)
                    start++;
                else if(start == end) {    //    start, end가 역전될 경우
                    start++;
                    end++;                 //    start, end 동시 증가
                    
                    if(end < N) {
                        sum = haybales[end][0];
                        map.put(haybales[end][1], 1);
                    }
                }
            }
            else {    //    맛의 합이 M 미만이라 식사 구성이 불가능할 경우
                end++;
                if(end < N) {
                    sum += haybales[end][0];
                    map.put(haybales[end][1], map.getOrDefault(haybales[end][1], 0) + 1);
                }
            }
        }
        
        System.out.println(answer);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 12869 뮤탈리스크

[12869번: 뮤탈리스크](http://boj.ma/12869/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Arrays;
import java.util.Queue;
import java.util.ArrayDeque;

public class Main {
    private static final int INF = Integer.MAX_VALUE;
    private static final int MAX = 61;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;

    private static int init = 0;
    private static int mult = 1;
    
    //    visited[x * 1 + y * 61 + z * 61^2] : SCV 체력이 x, y, z인 상태에 도달한 경우 true
    private static boolean[] visited;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        visited = new boolean[(int)Math.pow(MAX, N)];
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++) {
            int scv = Integer.parseInt(st.nextToken());
            init += scv * mult;
            mult *= MAX;
        }
        
        System.out.println(bfs());
    }    //    main-end
    
    private static int bfs() {
        Queue<int[]> queue = new ArrayDeque<>();
        visited[init] = true;
        queue.offer(new int[] {init, 0});
        
        while(!queue.isEmpty()) {
            int[] cur = queue.poll();
            int cstat = cur[0];
            int ccnt = cur[1];
            
            //    현재 상태의 scv 체력
            int c3 = cstat / (MAX * MAX);
            int c2 = (cstat % (MAX * MAX)) / MAX;
            int c1 = cstat % MAX; 
            
            int nstat = cstat;
            int n3 = c3;
            int n2 = c2;
            int n1 = c1;
            
            //    3번 scv 때리기
            if(c3 > 0) {
                n3 = Math.max(c3 - 9, 0);
                n2 = Math.max(c2 - 3, 0);
                n1 = Math.max(c1 - 1, 0);
                
                nstat = n1 * 1 + n2 * MAX + n3 * MAX * MAX;
                if(nstat == 0)
                    return ccnt + 1;
                
                if(!visited[nstat]) {
                    visited[nstat] = true;
                    queue.offer(new int[] {nstat, ccnt + 1});
                }
                
                n2 = Math.max(c2 - 1, 0);
                n1 = Math.max(c1 - 3, 0);
                nstat = n1 * 1 + n2 * MAX + n3 * MAX * MAX;
                if(nstat == 0)
                    return ccnt + 1;
                
                if(!visited[nstat]) {
                    visited[nstat] = true;
                    queue.offer(new int[] {nstat, ccnt + 1});
                }
            }
            //    2번 scv 때리기
            if(c2 > 0) {
                n3 = Math.max(c3 - 3, 0);
                n2 = Math.max(c2 - 9, 0);
                n1 = Math.max(c1 - 1, 0);
                
                nstat = n1 * 1 + n2 * MAX + n3 * MAX * MAX;
                if(nstat == 0)
                    return ccnt + 1;
                
                if(!visited[nstat]) {
                    visited[nstat] = true;
                    queue.offer(new int[] {nstat, ccnt + 1});
                }
                
                n3 = Math.max(c3 - 1, 0);
                n1 = Math.max(c1 - 3, 0);
                nstat = n1 * 1 + n2 * MAX + n3 * MAX * MAX;
                if(nstat == 0)
                    return ccnt + 1;
                
                if(!visited[nstat]) {
                    visited[nstat] = true;
                    queue.offer(new int[] {nstat, ccnt + 1});
                }
            }
            //    1번 scv 때리기
            if(c1 > 0) {
                n3 = Math.max(c3 - 3, 0);
                n2 = Math.max(c2 - 1, 0);
                n1 = Math.max(c1 - 9, 0);
                
                nstat = n1 * 1 + n2 * MAX + n3 * MAX * MAX;
                if(nstat == 0)
                    return ccnt + 1;
                
                if(!visited[nstat]) {
                    visited[nstat] = true;
                    queue.offer(new int[] {nstat, ccnt + 1});
                }
                
                n3 = Math.max(c3 - 1, 0);
                n2 = Math.max(c2 - 3, 0);
                nstat = n1 * 1 + n2 * MAX + n3 * MAX * MAX;
                if(nstat == 0)
                    return ccnt + 1;
                
                if(!visited[nstat]) {
                    visited[nstat] = true;
                    queue.offer(new int[] {nstat, ccnt + 1});
                }
            }
        }
        
        return -1;
    }
}   //    Main-class-end
```