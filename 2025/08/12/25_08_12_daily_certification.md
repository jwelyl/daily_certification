# 25_08_12_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2629 양팔저울

[2629번: 양팔저울](http://boj.ma/2629/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final int MAX = 40_000;    //    측정하고자 하는 구슬 무게의 최댓값
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;            //    추 개수
    private static int[] weights;    //    weights[nth] : nth번째 추 무게
    private static int T;            //    테스트할 구슬 개수
    
    //    visited[nth][w] : 1 ~ nth까지의 추를 이용해 무게 w의 구슬을 측정할 수 있으면 true
    private static boolean[][] visited;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        weights = new int[N + 1];
        visited = new boolean[N + 1][MAX + 1];
        st = new StringTokenizer(br.readLine());
        for(int nth = 1; nth <= N; nth++)
            weights[nth] = Integer.parseInt(st.nextToken());
        
        dfs(0, 0, 0);
        
        T = Integer.parseInt(br.readLine());
        st = new StringTokenizer(br.readLine());
        for(int tc = 1; tc <= T; tc++) {
            int w = Integer.parseInt(st.nextToken());
            sb.append(visited[N][w] ? "Y " : "N ");
        }
        
        System.out.println(sb);
    }    //    main-end 
    
    //    left/right : 양팔저울의 왼/오른쪽에 놓은 추의 무게 합
    //    구슬 무게를 w라고 하면 w + left = right 또는 w + right = left임
    //    즉 w = |right - left|
    private static void dfs(int nth, int left, int right) {
    	//System.out.println("dfs(" + nth + ", " + left + ", " + right + ")");
    	
        visited[nth][Math.abs(right - left)] = true;
        
        if(nth + 1 <= N) {    //    다음에 확인해볼 추가 있을 경우
            int w = weights[nth + 1];
            
            //    1. 무게 w인 추를 왼쪽에 놓는 경우
            if(!visited[nth + 1][Math.abs(right - (left + w))])
                dfs(nth + 1, left + w, right);
            //    2. 무게 w인 추를 오른쪽에 놓는 경우
            if(!visited[nth + 1][Math.abs((right + w) - left)])
                dfs(nth + 1, left, right + w);
            //    3. 무게 w인 추를 양쪽 모두 놓지 않는 경우
            if(!visited[nth + 1][Math.abs(right - left)])
                dfs(nth + 1, left, right);
        }
    }
}    //    Main-class-end
```

### BOJ 1135 뉴스 전하기

[1135번: 뉴스 전하기](http://boj.ma/1135/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Collections;

public class Main {
    private static final int ROOT = 0;    //    사장 번호
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    사장 포함 직원 수 (0 <= 직원 번호 < N)
    private static List<Employee>[] tree;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        tree = new ArrayList[N];
        
        for(int v = 0; v < N; v++)
            tree[v] = new ArrayList<>();
        
        st = new StringTokenizer(br.readLine());
        st.nextToken();
        for(int id = 1; id < N; id++) {
            int pid = Integer.parseInt(st.nextToken());
            tree[pid].add(new Employee(id));
        }
        
        System.out.println(dfs(ROOT));
    }    //    main-end
    
    private static int dfs(int id) {
        int res = 0;
        int time = 1;
        
        for(Employee next : tree[id])
            next.time = dfs(next.id);
        
        Collections.sort(tree[id]);
        for(Employee next : tree[id]) {    //    휘하 직원에게 뉴스 전파하는데 가장 오래 걸리는 직원에게 먼저 전파
            res = Math.max(res, next.time + time);
            time++;
        }
        
        return res;
    }
    
    private static class Employee implements Comparable<Employee> {
        public final int id;     //    직원 ID
        public int time;         //    해당 직원의 휘하 직원 중 가장 뉴스를 늦게 전달받은 직원이 뉴스 전달받는 시각
        
        public Employee(int id) {
            this.id = id;
        }
        
        @Override
        public int compareTo(Employee other) {
            return Integer.compare(other.time, this.time);
        }
    }
}    //    Main-class-end
```