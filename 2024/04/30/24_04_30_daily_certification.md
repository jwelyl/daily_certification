# 24_04_30_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 자주 등장한 top k 숫자**

[https://www.codetree.ai/missions/8/problems/top-k-frequent-elements/description](https://www.codetree.ai/missions/8/problems/top-k-frequent-elements/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/top-k-frequent-elements/description)

**코드**

```java
import java.util.*;
import java.io.*;
import java.util.Map.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer tokens;

    private static int n;   //  원소 개수
    private static int k;   //  자주 등장한 k개의 수

    private static final Map<Integer, Integer> map = new HashMap<>();
    
    private static final PriorityQueue<Num> pq = new PriorityQueue<>();

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());

        n = Integer.parseInt(tokens.nextToken());
        k = Integer.parseInt(tokens.nextToken());

        tokens = new StringTokenizer(br.readLine());
        for(int i = 0; i < n; i++) {
            int num = Integer.parseInt(tokens.nextToken());

            map.put(num, map.getOrDefault(num, 0) + 1);        
        }

        Set<Entry<Integer, Integer>> entrySet = map.entrySet();

        for(Entry<Integer, Integer> entry : entrySet)
            pq.offer(new Num(entry.getKey(), entry.getValue()));

        while(k-- > 0) {
            Num num = pq.poll();
            sb.append(num.num).append(" ");
        }

        System.out.println(sb);
    }   //  main-end

    private static class Num implements Comparable<Num> {
        int cnt;    //  등장 횟수
        int num;    //  등장 횟수

        public Num(int num, int cnt) {
            this.num = num;
            this.cnt = cnt;
        }

        @Override
        public int compareTo(Num num) {
            if(this.cnt == num.cnt)
                return Integer.compare(num.num, this.num);

            return Integer.compare(num.cnt, this.cnt);
        }
    }
}   //  Main-class-end
```

**CO{)E TREE 순서를 바꾸었을 때 같은 단어 그룹화하기**

[https://www.codetree.ai/missions/8/problems/group-same-word/submissions](https://www.codetree.ai/missions/8/problems/group-same-word/submissions)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/group-same-word/submissions)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final Map<String, Integer> map = new HashMap<>();

    private static int n;           //  단어 개수
    private static int maxCnt = 0;  //  가장 많이 등장한 그룹 단어 개수
    
    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());

        for(int i = 0; i < n; i++) {
            char[] input = br.readLine().toCharArray();
            Arrays.sort(input);
            String str = new String(input);

            map.put(str, map.getOrDefault(str, 0) + 1);

            maxCnt = Math.max(maxCnt, map.get(str));
        }

        System.out.println(maxCnt);
    }   //  main-end
}   //  Main-class-end
```