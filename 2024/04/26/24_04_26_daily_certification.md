# 24_04_26_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE bit 계산**

[https://www.codetree.ai/missions/9/problems/bit-calculation/introduction](https://www.codetree.ai/missions/9/problems/bit-calculation/introduction)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/9/problems/bit-calculation/introduction)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final int MAX = (1 << 30) - 1;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;
    private static final StringBuilder sb = new StringBuilder();

    private static int q = 0;
    private static int set = 0;

    public static void main(String[] args) throws IOException {
        q = Integer.parseInt(br.readLine());

        for(int i = 0; i < q; i++) {
            tokens = new StringTokenizer(br.readLine());
            String opt = tokens.nextToken();
            int x = -1;
            int key = -1;

            switch(opt) {
            case "add":
                x = Integer.parseInt(tokens.nextToken()) - 1;
                
                key = 1 << x;
                set = set | key;
                
                break;
            case "delete":
                x = Integer.parseInt(tokens.nextToken()) - 1;

                key = MAX ^ (1 << x);
                set = set & key;

                break;
            case "print":
                x = Integer.parseInt(tokens.nextToken()) - 1;
                
                key = 1 << x;

                sb.append((set & key) == 0 ? 0 : 1).append("\n"); 
                break;
            case "toggle":
                x = Integer.parseInt(tokens.nextToken()) - 1;

                key = 1 << x;
                set = set ^ key;

                break;
            default:
                set = 0;
            }
        }

        System.out.print(sb);
    }   //  main-end
}   //  Main-class-end
```

**CO{)E TREE 겹치는 bit가 없는 세 수**

[https://www.codetree.ai/missions/9/problems/three-numbers-with-no-overlapping-bits/description](https://www.codetree.ai/missions/9/problems/three-numbers-with-no-overlapping-bits/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/9/problems/three-numbers-with-no-overlapping-bits/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n = 0;
    private static int[] nums;

    private static int maxSum = 0;

    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());

        nums = new int[n];
        tokens = new StringTokenizer(br.readLine());

        for(int i = 0; i < n; i++)
            nums[i] = Integer.parseInt(tokens.nextToken());

        for(int i = 0; i < n - 2; i++) {
            for(int j = i + 1; j < n - 1; j++) {
                for(int k = j + 1; k < n; k++) {
                    int num1 = nums[i];
                    int num2 = nums[j];
                    int num3 = nums[k];

                    if((num1 & num2) == 0 && (num2 & num3) == 0 && (num3 & num1) == 0)
                        maxSum = Math.max(maxSum, num1 + num2 + num3);
                }
            }
        }

        System.out.println(maxSum);
    }   //  main-end
}   //  Main-class-end
```

**CO{)E TREE 트리의 부모 노드**

[https://www.codetree.ai/missions/9/problems/parent-node-of-the-tree/description](https://www.codetree.ai/missions/9/problems/parent-node-of-the-tree/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/9/problems/parent-node-of-the-tree/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final int ROOT = 1;

    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;
    private static final StringBuilder sb = new StringBuilder();

    private static int n;
    private static List<Integer>[] tree;
    private static int[] parents;

    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());

        tree = new ArrayList[n + 1];
        for(int i = 0; i <= n; i++)
            tree[i] = new ArrayList<>();
        parents = new int[n + 1];
        Arrays.fill(parents, -1);

        for(int i = 0; i < n - 1; i++) {
            tokens = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(tokens.nextToken());
            int v2 = Integer.parseInt(tokens.nextToken());

            tree[v1].add(v2);
            tree[v2].add(v1);
        }

        dfs(ROOT, 0);

        for(int i = 2; i <= n; i++)
            sb.append(parents[i]).append("\n");

        System.out.println(sb);
    }   //  main-end

    private static void dfs(int cur, int prev) {
        parents[cur] = prev;

        for(int next : tree[cur]) {
            if(parents[next] == -1)
                dfs(next, cur);
        }
    }
}   //  Main-class-end
```

**CO{)E TREE 집합의 원소**

[https://www.codetree.ai/missions/9/problems/elements-of-a-set/description](https://www.codetree.ai/missions/9/problems/elements-of-a-set/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/9/problems/elements-of-a-set/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;
    private static final StringBuilder sb = new StringBuilder();

    private static int n;
    private static int m;
    private static int[] parents;

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        m = Integer.parseInt(tokens.nextToken());

        parents = new int[n + 1];
        for(int i = 0; i <= n; i++)
            parents[i] = i;

        for(int i = 0; i < m; i++) {
            tokens = new StringTokenizer(br.readLine());
            int opt = Integer.parseInt(tokens.nextToken());
            int v1 = Integer.parseInt(tokens.nextToken());
            int v2 = Integer.parseInt(tokens.nextToken());

            v1 = findDS(v1);
            v2 = findDS(v2);

            if(opt == 1)
                sb.append((v1 == v2) ? 1 : 0).append("\n");
            else
                union(v1, v2);
        }

        System.out.print(sb);
    }   //  main-end

    private static int findDS(int v) {
        if(v == parents[v])
            return v;

        return parents[v] = findDS(parents[v]);
    }

    private static void union(int v1, int v2) {
        if(v1 != v2)
            parents[v2] = v1;
    }
}   //  Main-class-end
```