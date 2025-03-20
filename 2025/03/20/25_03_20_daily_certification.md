# 25_03_20_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 21939 문제 추천 시스템 Version 1

[21939번: 문제 추천 시스템 Version 1](http://boj.ma/21939/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.HashMap;
import java.util.TreeSet;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    //    해당 문제 (난이도 순, 문제 번호 순으로 오름차순 정렬)
    private static final TreeSet<Problem> problemList = new TreeSet<>();
    //    Key : 문제 번호, Value : 해당 문제
    private static final HashMap<Integer, Problem> problemMap = new HashMap<>();
    
    private static int N;    //    초기에 주어진 문제 수
    private static int M;    //    쿼리 수
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        for(int i = 0; i < N; i++) {
            st = new StringTokenizer(br.readLine());
            int p = Integer.parseInt(st.nextToken());
            int l = Integer.parseInt(st.nextToken());
            
            Problem prob = new Problem(p, l);
            
            problemList.add(prob);
            problemMap.put(p, prob);
        }
        
        M = Integer.parseInt(br.readLine());
        for(int q = 0; q < M; q++) {
            st = new StringTokenizer(br.readLine());
            String query = st.nextToken();
            Problem prob = null;
            
            if(query.equals("recommend")) {
                int x = Integer.parseInt(st.nextToken());
                
                if(x == 1)    //    가장 어려운 문제 고르기, 가장 어려운 문제가 여럿이면 그 중 문제 번호가 가장 큰 문제 고르기
                    prob = problemList.last();
                else          //    가장 쉬운 문제 고르기, 가장 쉬운 문제가 여럿이면 그 중 문제 번호가 가장 작은 문제 고르기
                    prob = problemList.first();
            
                sb.append(prob.p).append("\n");
            }
            else if(query.equals("add")) {
                int p = Integer.parseInt(st.nextToken());
                int l = Integer.parseInt(st.nextToken());
                
                prob = new Problem(p, l);
                
                problemList.add(prob);
                problemMap.put(p, prob);
            }
            else {
                int p = Integer.parseInt(st.nextToken());
                
                prob = problemMap.get(p);    //    문제 번호 p인 문제 찾기
                problemList.remove(prob);    //    해당 문제 지우기
            }
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static class Problem implements Comparable<Problem> {
        public int p;    //    문제 번호
        public int l;    //    문제 난이도
        
        public Problem(int p, int l) {
            this.p = p;
            this.l = l;
        }
        
        @Override
        public int compareTo(Problem other) {
            int res = Integer.compare(this.l, other.l);    //    난이도에 대해 오름차순 정렬
            
            if(res == 0)
                res = Integer.compare(this.p, other.p);    //    난이도가 같을 경우 문제 번호에 대해 오름차순 정렬
            
            return res;
        }
    }
}    //    Main-class-end
```

### BOJ 21944 문제 추천 시스템 Version 2

[21944번: 문제 추천 시스템 Version 2](http://boj.ma/21944/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.HashMap;
import java.util.TreeSet;

public class Main {
    private static final int MIN_P = 1;             //    가장 낮은 문제 번호
    private static final int MAX_P = 100_000;       //    가장 높은 문제 번호
    private static final int MAX_ALGO = 100;        //    알고리즘 종류 최대 100개
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    //    전체 문제 리스트 (난이도 순, 문제 번호 순으로 오름차순 정렬)
    private static final TreeSet<Problem> allProblemList = new TreeSet<>();
    //    algoProblemList[i] : 알고리즘 i에 해당하는 문제 리스트 (난이도 순, 문제 번호 순으로 오름차순 정렬)
    private static final TreeSet<Problem>[] algoProblemList = new TreeSet[MAX_ALGO + 1];
    //    Key : 문제 번호, Value : 해당 문제
    private static final HashMap<Integer, Problem> problemMap = new HashMap<>();
    
    private static int N;    //    초기에 주어진 문제 수
    private static int M;    //    쿼리 수
    
    public static void main(String[] args) throws IOException {
        for(int i = 1; i <= MAX_ALGO; i++)
            algoProblemList[i] = new TreeSet<>();
        
        N = Integer.parseInt(br.readLine());
        
        for(int i = 0; i < N; i++) {
            st = new StringTokenizer(br.readLine());
            int p = Integer.parseInt(st.nextToken());
            int l = Integer.parseInt(st.nextToken());
            int g = Integer.parseInt(st.nextToken());
            
            Problem prob = new Problem(p, l, g);
            
            allProblemList.add(prob);
            algoProblemList[g].add(prob);
            problemMap.put(p, prob);
        }
        
        M = Integer.parseInt(br.readLine());
        for(int q = 0; q < M; q++) {
            st = new StringTokenizer(br.readLine());
            String query = st.nextToken();
            Problem prob = null;
            int p = 0;
            int g = 0;
            int x = 0;
            int l = 0;
            
            if(query.equals("recommend")) {
                g = Integer.parseInt(st.nextToken());
                x = Integer.parseInt(st.nextToken());
                
                if(x == 1)    //    알고리즘 분류가 g인 문제 중 가장 어려운 문제 고르기, 가장 어려운 문제가 여럿이면 그 중 문제 번호가 가장 큰 문제 고르기
                    prob = algoProblemList[g].last();
                else          //    알고리즘 분류가 g인 문제 중 가장 쉬운 문제 고르기, 가장 쉬운 문제가 여럿이면 그 중 문제 번호가 가장 작은 문제 고르기
                    prob = algoProblemList[g].first();
            
                sb.append(prob.p).append("\n");
            }
            else if(query.equals("recommend2")) {
                x = Integer.parseInt(st.nextToken());
                
                if(x == 1)    //    전체 문제 중 가장 어려운 문제 고르기, 가장 어려운 문제가 여럿이면 그 중 문제 번호가 가장 큰 문제 고르기
                    prob = allProblemList.last();
                else          //    전체 문제 중 가장 쉬운 문제 고르기, 가장 쉬운 문제가 여럿이면 그 중 문제 번호가 가장 작은 문제 고르기
                    prob = allProblemList.first();
            
                sb.append(prob.p).append("\n");
            }
            else if(query.equals("recommend3")) {
                x = Integer.parseInt(st.nextToken());
                l = Integer.parseInt(st.nextToken());

                Problem dummy = new Problem();    //    검색을 위한 더미 문제
                dummy.l = l;
                
                if(x == 1) {   //    전체 문제 중 난이도가 l보다 크거나 같은 문제 중 가장 쉬운 문제 찾기
                    dummy.p = MIN_P - 1;    //    난이도가 l인 문제들이 존재할 경우, 가장 문제 번호가 작은 문제 나오게 하기 위해
                    prob = allProblemList.ceiling(dummy);
                }
                else {          //    전체 문제 중 난이도가 l보다 작거나 같은 문제 중 가장 어려운 문제 찾기
                    dummy.p = MIN_P - 1;    //    난이도가 l인 문제들이 존재할 경우, 가장 문제 번호가 큰 문제 나오게 하기 위해
                    prob = allProblemList.floor(dummy);
                }
            
                sb.append(prob == null ? -1 : prob.p).append("\n");
            }
            else if(query.equals("add")) {
                p = Integer.parseInt(st.nextToken());
                l = Integer.parseInt(st.nextToken());
                g = Integer.parseInt(st.nextToken());
                
                prob = new Problem(p, l, g);
                
                allProblemList.add(prob);
                algoProblemList[g].add(prob);
                problemMap.put(p, prob);
            }
            else {
                p = Integer.parseInt(st.nextToken());
                
                prob = problemMap.get(p);    //    문제 번호 p인 문제 찾기
                g = prob.g;
                
                algoProblemList[g].remove(prob);
                allProblemList.remove(prob);    //    전체 리스트에서 해당 문제 지우기
            }
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static class Problem implements Comparable<Problem> {
        public int p;    //    문제 번호
        public int l;    //    문제 난이도
        public int g;    //    문제 알고리즘
        
        public Problem() {}
        
        public Problem(int p, int l, int g) {
            this.p = p;
            this.l = l;
            this.g = g;
        }
        
        @Override
        public int compareTo(Problem other) {
            int res = Integer.compare(this.l, other.l);    //    난이도에 대해 오름차순 정렬
            
            if(res == 0)
                res = Integer.compare(this.p, other.p);    //    난이도가 같을 경우 문제 번호에 대해 오름차순 정렬
            
            return res;
        }
    }
}    //    Main-class-end
```

### BOJ 17430 가로등

[17430번: 가로등](http://boj.ma/17430/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.Set;
import java.util.HashSet;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int T;
    private static int N;
    
    private static final Set<Integer> xSet = new HashSet<>();
    private static final Set<Integer> ySet = new HashSet<>();
    
    public static void main(String[] args) throws IOException {
        T = Integer.parseInt(br.readLine());
        
        for(int tc = 1; tc <= T; tc++) {
            N = Integer.parseInt(br.readLine());
            
            xSet.clear();
            ySet.clear();
            
            for(int i = 0; i < N; i++) {
                st = new StringTokenizer(br.readLine());
                int x = Integer.parseInt(st.nextToken());
                int y = Integer.parseInt(st.nextToken());
                
                xSet.add(x);
                ySet.add(y);
            }
            
            sb.append(xSet.size() * ySet.size() == N ? "BALANCED\n" : "NOT BALANCED\n");
        }
        
        System.out.print(sb);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 2295 **세 수의 합**

[2295번: 세 수의 합](http://boj.ma/2295/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.Map;
import java.util.HashMap;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    //    Key : arr[k] - arr[z], Value : arr[k] - arr[z]에 해당하는 k 값 중 최댓값
    private static final Map<Integer, Integer> map = new HashMap<>();
    
    private static int N;
    private static int[] arr;
    
    private static int answer = 0;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        arr = new int[N];
        
        for(int i = 0; i < N; i++)
            arr[i] = Integer.parseInt(br.readLine());
        
        Arrays.sort(arr);
        
        for(int k = N - 1; k > 0; k--) {
            for(int z = k - 1; z >= 0; z--) {
                int diff = arr[k] - arr[z];
                
                if(map.getOrDefault(diff, -1) < k)
                    map.put(diff, k);
            }
        }
        
        for(int x = 0; x < N - 1; x++) {
            for(int y = x; y < N - 1; y++) {
                int sum = arr[x] + arr[y];
                
                int k = map.getOrDefault(sum, -1);
                
                if(k != -1)
                    answer = Math.max(answer, arr[k]);
            }
        }
        
        System.out.println(answer);
    }    //    main-end
}    //    Main-class-end
```