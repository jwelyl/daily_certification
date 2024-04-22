# 24_04_23_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 가장 짧은 부분합**

[https://www.codetree.ai/missions/8/problems/shortest-subtotal/introduction](https://www.codetree.ai/missions/8/problems/shortest-subtotal/introduction)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/shortest-subtotal/introduction)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;   //  원소 개수
    private static int s;   //  원소 합 s

    private static int[] nums;  //  원소

    private static int start = 0;
    private static int end = 0;
    private static int sum = 0;

    private static int len = Integer.MAX_VALUE; //  원소들 합이 s 이상이 되는 가장 짧은 구간 길이
    
    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        s = Integer.parseInt(tokens.nextToken());

        nums = new int[n];
        tokens = new StringTokenizer(br.readLine());

        for(int i = 0; i < n; i++)
            nums[i] = Integer.parseInt(tokens.nextToken());

        sum = nums[end];

        while(start <= end && end < n) {
            if(sum >= s) {   // 구간 합이 s 이상일 경우
                // System.out.println("start = " + start + ", end = " + end);
                // System.out.println("sum = " + sum);

                len = Math.min(len, end - start + 1);
                sum -= nums[start];
                start++;
            }
            else {  //  구간 합이 s 미만일 경우
                end++;
                if(end < n)
                    sum += nums[end];
            }
        }

        System.out.println(len == Integer.MAX_VALUE ? -1 : len);
    }   //  main-end
}   //  Main-class-end
```

**CO{)E TREE 연속하는 정수 n개의 합**

[https://www.codetree.ai/missions/8/problems/sum-of-consecutive-n-integers/description](https://www.codetree.ai/missions/8/problems/sum-of-consecutive-n-integers/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/sum-of-consecutive-n-integers/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;   //  원소 개수
    private static int m;   //  원소 합 s

    private static int[] nums;  //  원소

    private static int start = 0;
    private static int end = 0;
    private static int sum = 0;

    private static int ans = 0; //  원소들 합이 m이 되는 경우의 수
    
    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        m = Integer.parseInt(tokens.nextToken());

        nums = new int[n];
        tokens = new StringTokenizer(br.readLine());

        for(int i = 0; i < n; i++)
            nums[i] = Integer.parseInt(tokens.nextToken());

        sum = nums[end];

        while(start <= end && end < n) {
            if(sum == m) {
                ans++;
                sum -= nums[start];
                start++;
                end++;
                if(end < n)
                    sum += nums[end];
            }
            else if(sum > m) {
                sum -= nums[start];
                start++;
            }
            else {
                end++;
                if(end < n)
                    sum += nums[end];
            }
        }

        System.out.println(ans);
    }   //  main-end
}   //  Main-class-end
```

**CO{)E TREE 정수 두 개의 합 2**

[https://www.codetree.ai/missions/8/problems/sum-of-two-integers-2/description](https://www.codetree.ai/missions/8/problems/sum-of-two-integers-2/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/sum-of-two-integers-2/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;   //  원소 개수
    private static int k;   //  원소 합 k

    private static int[] nums;  //  원소

    private static int ans = 0; //  원소들 합이 m이 되는 경우의 수
    
    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        k = Integer.parseInt(tokens.nextToken());

        nums = new int[n];

        for(int i = 0; i < n; i++)
            nums[i] = Integer.parseInt(br.readLine());

        Arrays.sort(nums);

        for(int i = 0; i < n - 1; i++) {
            int num = nums[i];

            if(k - num < num)
                break;

            int from = i + 1;
            int to = n - 1;
            int target = k - num;
            
            ans += binarySearch(from, to, target);  //  [from, to] 범위에서 target 이하 정수 개수 찾기
        }

        System.out.println(ans);
    }   //  main-end

    private static int binarySearch(int from, int to, int target) {
        int idx = -1;

        int start = from;
        int end = to;

        while(start <= end) {
            int mid = (start + end) / 2;

            if(nums[mid] <= target) {
                idx = mid;
                start = mid + 1;
            }
            else end = mid - 1;
        }

        return idx == -1 ? 0 : idx - from + 1;
    }
}   //  Main-class-end
```