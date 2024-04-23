# 24_04_23_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 연속 부분 합의 최댓값 구하기 2**

[https://www.codetree.ai/missions/8/problems/max-of-partial-sum-2/description](https://www.codetree.ai/missions/8/problems/max-of-partial-sum-2/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/max-of-partial-sum-2/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;
    private static int[] nums;

    private static int start = 0;
    private static int end = 0;
    private static int sum = 0;
    private static int maxSum = Integer.MIN_VALUE;

    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());

        nums = new int[n];
        tokens = new StringTokenizer(br.readLine());
        for(int i = 0; i < n; i++)
            nums[i] = Integer.parseInt(tokens.nextToken());

        sum = nums[0];
        maxSum = Math.max(maxSum, sum);

        while(end < n) {
            if(sum < 0) {   //  합이 음수가 될 경우
                if(start < n - 1 && end < n - 1) {;
                    start++;
                    sum = nums[end + 1];
                    end++;
                }
                else break; //  더 이상 갈 수 없을 경우
            }
            else {  //  합이 0 이상일 경우
                end++;
                if(end < n)
                    sum += nums[end];
            }

            maxSum = Math.max(maxSum, sum);
        }

        System.out.println(maxSum);
    }   //  main-end
}   //  Main-class-end
```

**CO{)E TREE 쪼개어 배낭 채우기 구현**

[https://www.codetree.ai/missions/8/problems/implement-fractional-knapsack/description](https://www.codetree.ai/missions/8/problems/implement-fractional-knapsack/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/implement-fractional-knapsack/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;   //  보석 개수
    private static int m;   //  가방 무게

    private static final List<Gem> gemList = new ArrayList<>();

    private static double maxValue = 0.0;

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        m = Integer.parseInt(tokens.nextToken());

        for(int i = 0; i < n; i++) {
            tokens = new StringTokenizer(br.readLine());

            gemList.add(new Gem(Integer.parseInt(tokens.nextToken()), Double.parseDouble(tokens.nextToken())));
        }

        Collections.sort(gemList);

        for(Gem gem : gemList) {
            int w = gem.w;
            double v = gem.v;
            double partial = v / w;

            if(w < m) {
                maxValue += v;
                m -= w;
            } else {
                maxValue += (v / w) * m;
                break;
            }
        }

        System.out.printf("%.3f\n", maxValue);
    }   //  main-end

    private static class Gem implements Comparable<Gem> {
        int w;
        double v;

        public Gem(int w, double v) {
            this.w = w;
            this.v = v;
        }

        @Override
        public int compareTo(Gem gem) {
            return Double.compare(gem.v / gem.w, this.v / this.w);
        }
    }
}   //  Main-class-end
```

**CO{)E TREE 숫자 합치기**

[https://www.codetree.ai/missions/8/problems/merge-numbers/description](https://www.codetree.ai/missions/8/problems/%08merge-numbers/description)

[https://www.codetree.ai/missions/8/problems/merge-numbers/description](https://www.codetree.ai/missions/8/problems/%08merge-numbers/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;   //  수 개수

    private static final PriorityQueue<Integer> pq = new PriorityQueue<>();

    private static int ans = 0;

    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());

        tokens = new StringTokenizer(br.readLine());
        for(int i = 0; i < n; i++)
            pq.offer(Integer.parseInt(tokens.nextToken()));

        while(pq.size() >= 2) {
            int num1 = pq.poll();
            int num2 = pq.poll();

            ans += (num1 + num2);
            pq.offer(num1 + num2);
        }

        System.out.println(ans);
    }   //  main-end
}   //  Main-class-end
```