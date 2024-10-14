# 24_10_14_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16437 양 구출 작전

[](https://www.acmicpc.net/problem/16437)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;

public class Main {
    private static final int ROOT = 1;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;              //    정점 수
    
    private static List<Integer>[] tree;
    private static boolean[] sheep;        //    true면 양, false면 늑대
    private static long[] cnts;            //    cnts[v] : 정점 v에 있는 양 또는 늑대 수
    private static boolean[] visited;      //    방문 처리 배열    
    
    private static long ans;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        tree = new ArrayList[N + 1];
        for(int v = 1; v <= N; v++)
            tree[v] = new ArrayList<>();
        sheep = new boolean[N + 1];
        cnts = new long[N + 1];
        visited = new boolean[N + 1];
        
        for(int v = 2; v <= N; v++) {
            st = new StringTokenizer(br.readLine());
            
            String kind = st.nextToken();
            int a = Integer.parseInt(st.nextToken());
            int p = Integer.parseInt(st.nextToken());
            
            sheep[v] = kind.equals("S");
            cnts[v] = a;
            
            tree[p].add(v);
            tree[v].add(p);
        }
        
        System.out.println(postorder(ROOT));
    }    //    main-end
    
    //    v까지 도달할 수 있는 양의 수
    private static long postorder(int v) {
        long res = sheep[v] ? cnts[v] : 0;    //    v에 양이 있을 경우 있는 수 그대로, 늑대일 경우, 일단 0마리
        
        visited[v] = true;
        
        for(int child : tree[v]) {
            if(!visited[child]) {    //    자식 노드일 경우
                long childRes = postorder(child);    //    child까지 올 수 있는 양의 수
                
                if(!sheep[v]) {    //    v가 늑대일 경우
                    long remain = childRes - cnts[v];    //    v에 있는 늑대에게 잡아먹히고 남은 양의 수
                    
                    cnts[v] = cnts[v] - childRes <= 0 ? 0 : cnts[v] - childRes;    //    양 잡아먹고 남은 늑대 수
                    
                    if(remain > 0)    //    늑대에게 잡아먹히고 남은 양이 있을 경우
                        res += remain;
                }
                else    //    v가 양일 경우
                    res += childRes;    //    child까지 올 수 있는 양의 수를 그대로 더함
            }
        }
        
        return res;
    }
}    //    Main-class-end
```

### BOJ 5021 왕위 계승

[](https://www.acmicpc.net/problem/5021)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Map;
import java.util.HashMap;
import java.util.List;
import java.util.ArrayList;
import java.util.Queue;
import java.util.LinkedList;

public class Main {
    private static final int MAX_N = 50 * 3 + 1;    //    최대 인원 수
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    //    Key : 사람 이름, Value : 정점 번호
    private static final Map<String, Integer> nameMap = new HashMap<>();
    
    private static int N;    //    가족 관계 정보
    private static int M;    //    왕위 계승 요구자
    private static int num = 0;    //    사람 번호
    
    private static final List<Integer>[] graph = new ArrayList[MAX_N];
    private static final int[] indegrees = new int[MAX_N];
    private static final double[] bloods = new double[MAX_N];
    
    private static double maxBlood = 0.0;
    private static String maxBloodName = "";
    
    public static void main(String[] args) throws IOException {
        for(int v = 0; v < MAX_N; v++)
            graph[v] = new ArrayList<>();
        
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        String starter = br.readLine();    //    창업 군주 이름
        nameMap.put(starter, num);
        bloods[num++] = 1.0;    //    창업 군주는 1임
        
        for(int i = 0; i < N; i++) {
            st = new StringTokenizer(br.readLine());

            String child = st.nextToken();    //    자식 이름
            
            int childNum = -1;    //    자식 번호 정하기
            
            if(nameMap.containsKey(child))    //    이전에 등장한 적 있는 사람일 경우
            	childNum = nameMap.get(child);
            else {    //    새로 등장한 사람일 경우
            	childNum = num++;
                nameMap.put(child, childNum);
            } 
            
            String p1 = st.nextToken();    //    부모 1 이름
            int p1Num = -1;
            
            if(nameMap.containsKey(p1))    //    이전에 등장한 적 있는 사람일 경우
                p1Num = nameMap.get(p1);
            else {    //    새로 등장한 사람일 경우
                p1Num = num++;
                nameMap.put(p1, p1Num);
            } 
            
            String p2 = st.nextToken();    //    부모 2 이름
            int p2Num = -1;
            
            if(nameMap.containsKey(p2))    //    이전에 등장한 적 있는 사람일 경우
                p2Num = nameMap.get(p2);
            else {    //    새로 등장한 사람일 경우
                p2Num = num++;
                nameMap.put(p2, p2Num);
            }

            graph[p1Num].add(childNum);
            indegrees[childNum]++;
            graph[p2Num].add(childNum);
            indegrees[childNum]++;
        }
        
        topologicalSort();
        
        for(int m = 0; m < M; m++) {
            String cand = br.readLine();                     //    왕위 계승 요구자
            int candNum = nameMap.getOrDefault(cand, -1);    //    왕위 계승 요구자 번호
            
            if(candNum != -1) {
                if(maxBlood < bloods[candNum]) {
                    maxBlood = bloods[candNum];
                    maxBloodName = cand;
                }
            }
        }
        
        System.out.println(maxBloodName);
    }    //    main-end
    
    private static void topologicalSort() {
        Queue<Integer> q = new LinkedList<>();
        
        for(int v = 0; v < num; v++) {    //    부모가 없는 사람들부터 넣기
            if(indegrees[v] == 0)
                q.offer(v);
        }
        
        while(!q.isEmpty()) {
            int parent = q.poll();    // 부모 확인

            for(int child : graph[parent]) {   // 자식들 확인
                bloods[child] += bloods[parent] / 2;  // 부모의 피를 절반 상속
                indegrees[child]--;  // 부모 한 명 확인 완료

                if (indegrees[child] == 0) 	// 부모 둘 다 확인했을 경우
                    q.offer(child);  // 자식을 큐에 추가
            }
        }
    }
}    //    Main-class-end
```