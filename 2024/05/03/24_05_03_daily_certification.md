# 24_05_03_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 자리 바꾸기 2**

[https://www.codetree.ai/missions/8/problems/changing-seats-2/description](https://www.codetree.ai/missions/8/problems/changing-seats-2/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/changing-seats-2/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer tokens;

    private static int n;   //  사람 수
    private static int k;   //  자리 바꾸는 횟수 3 * k

    private static int[] a;
    private static int[] b; //  자리 바꿀 위치 a, b

    private static int[] pos;   //  i번째 위치에 있는 사람 번호

    private static Set<Integer>[] sets;

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        
        n = Integer.parseInt(tokens.nextToken());
        k = Integer.parseInt(tokens.nextToken());
        
        a = new int[k];
        b = new int[k];
        pos = new int[n + 1];

        sets = new Set[n + 1];
        for(int i = 1; i <= n; i++)
            sets[i] = new HashSet<>();

        for(int i = 1; i <= n; i++) {
            pos[i] = i;
            sets[pos[i]].add(i);
        }

        for(int i = 0; i < k; i++) {
            tokens = new StringTokenizer(br.readLine());
            a[i] = Integer.parseInt(tokens.nextToken());
            b[i] = Integer.parseInt(tokens.nextToken());
        }

        for(int i = 0; i < 3 * k; i++) {
            int aIdx = a[i % k];    //  a 위치
            int bIdx = b[i % k];    //  b 위치

            int aNum = pos[aIdx];   //  a 위치에 있는 사람
            int bNum = pos[bIdx];   //  b 위치에 있는 사람

            sets[aNum].add(bIdx);   //  a 위치에 있는 사람의 위치에 b 위치 추가
            sets[bNum].add(aIdx);   //  b 위치에 있는 사람의 위치에 a 위치 추가

            pos[bIdx] = aNum;
            pos[aIdx] = bNum;   //  두 사람 자리 바꾸기
        }

        for(int i = 1; i <= n; i++)
            sb.append(sets[i].size()).append("\n");
    
        System.out.print(sb);
    }   //  main-end
}   //  Main-class-end
```