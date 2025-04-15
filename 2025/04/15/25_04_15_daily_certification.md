# 25_04_15_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 24461 그래프의 줄기

[24461번: 그래프의 줄기](http://boj.ma/24461/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
    private static int N;
    private static List<Integer>[] tree;
    private static int[] indegrees;
    private static boolean[] deleted;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());

        tree = new ArrayList[N];
        indegrees = new int[N];
        deleted = new boolean[N];

        for (int v = 0; v < N; v++)
            tree[v] = new ArrayList<>();

        for (int e = 0; e < N - 1; e++) {
        	st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            tree[v1].add(v2);
            tree[v2].add(v1);
            indegrees[v1]++;
            indegrees[v2]++;
        }

        topologicalSort();
    }	//	main-end

    private static void topologicalSort() {
        Queue<Integer> queue = new LinkedList<>();

        for (int v = 0; v < N; v++) {
            if (indegrees[v] == 1) {
                queue.add(v);
                deleted[v] = true;
            }
        }

        while (true) {
            int size = queue.size();
            if (size <= 2)	//	줄기만 남았으면
            	break;

           while(size-- > 0) {
                int cv = queue.poll();
                indegrees[cv]--;
                for (int nv : tree[cv]) {
                    if (!deleted[nv]) {
                    	indegrees[nv]--;
                    	
                    	if(indegrees[nv] == 1) {
                    		deleted[nv] = true;
                    		queue.add(nv);
                    	}
                    }
                }
            }
        }

        for (int v = 0; v < N; v++) {
            if (indegrees[v] != 0)
                sb.append(v).append(" ");
        }
        
        System.out.println(sb);
    }
}	//	Main-class-end
```