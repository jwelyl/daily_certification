# 24_05_04_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE C-TAG**

[https://www.codetree.ai/missions/8/problems/c-tag/description](https://www.codetree.ai/missions/8/problems/c-tag/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/c-tag/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;   //  종이 개수 2 * n
    private static int m;   //  종이 당 알파벳 개수 m

    private static final Set<String> set = new HashSet<>();
    private static char[][] strs;

    private static int ans = 0;

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());

        n = Integer.parseInt(tokens.nextToken());
        m = Integer.parseInt(tokens.nextToken());

        strs = new char[2 * n][m];

        for(int i = 0; i < 2 * n; i++)
            strs[i] = br.readLine().toCharArray();

        for(int i = 0; i < m - 2; i++) {
            for(int j = i + 1; j < m - 1; j++) {
                for(int k = j + 1; k < m; k++) {
                    set.clear();

                    //  처음 n개 종이의 3개 자리로 만든 문자열 set에 저장 (A 그룹)
                    for(int nth = 0; nth < n; nth++) {
                        StringBuilder sb = new StringBuilder("");
                        sb.append(strs[nth][i]).append(strs[nth][j]).append(strs[nth][k]);

                        set.add(sb.toString());
                    }

                    //  나중 n개 종이의 3개 자리로 만든 문자열이 set에 있나 확인 (B 그룹)
                    //  없으면 이 3개 자리로 2 * n개의 카드를 A, B 그룹으로 나눌 수 있음
                    boolean isOk = true;

                    for(int nth = n; nth < 2 * n; nth++) {
                        StringBuilder sb = new StringBuilder("");
                        sb.append(strs[nth][i]).append(strs[nth][j]).append(strs[nth][k]);

                        if(set.contains(sb.toString())) {   //  B 그룹의 3개 자리로 만든 문자열이 이미 A 그룹에도 존재할 경우
                            isOk = false;   //  나눌 수 없음
                            break;
                        }
                    }

                    if(isOk)    //  나눌 수 있을 경우
                        ans++;
                }
            }
        }

        System.out.println(ans);
    }   //  main-end
}   //  Main-class-end
```