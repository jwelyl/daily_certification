# 24_04_28_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 두 수의 합**

[https://www.codetree.ai/missions/8/problems/sum-of-two-num/description](https://www.codetree.ai/missions/8/problems/sum-of-two-num/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/sum-of-two-num/description)

주어진 수를 정렬할 수 있다는 것만 캐치하면 이분탐색 풀이를 떠올리는 건 어렵지 않다. 대신 이분탐색으로 특정 수의 개수를 구하기 위해서 효율적인 이분탐색을 구현해야 한다.

원래 문제의 의도인 해시맵으로 풀이하기 위해서는 조금 아이디어가 필요하다

주어진 수를 첫 번째 수부터 확인하며 해당 수의 보수가 이전에 몇 개 있었는지 해시맵으로 확인한 후, 현재 수를 해시 맵에 추가해준다.

**코드 (이분탐색 풀이)**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;   //  수 개수
    private static int k;   //  두 수 합 k
    private static final List<Integer> nums = new ArrayList<Integer>();    //  수 목록

    private static long ans = 0L;
    
    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        k = Integer.parseInt(tokens.nextToken());

        tokens = new StringTokenizer(br.readLine());
        for(int i = 0; i < n; i++)
            nums.add(Integer.parseInt(tokens.nextToken()));

        Collections.sort(nums);

        for(int i = 0; i < n - 1; i++) {
            int num = nums.get(i);
            int target = k - num;

            if(target >= num)
                ans += countOccurrences(target, i + 1, n - 1);
        }

        System.out.println(ans);
    }   //  main-end

    public static int countOccurrences(int target, int start, int end) {
        int firstIndex = findFirstIndex(target, start, end);
        int lastIndex = findLastIndex(target, start, end);
        
        if (firstIndex == -1 || lastIndex == -1) 
            return 0;
        
        return lastIndex - firstIndex + 1;
    }

    private static int findFirstIndex(int target, int start, int end) {
        int index = -1;
        while (start <= end) {
            int mid = start + (end - start) / 2;
            if (nums.get(mid) >= target) {
                if (nums.get(mid) == target) index = mid;
                end = mid - 1;
            } else
                start = mid + 1;
        }
        return index;
    }

    private static int findLastIndex(int target, int start, int end) {
        int index = -1;
        while (start <= end) {
            int mid = start + (end - start) / 2;
            if (nums.get(mid) <= target) {
                if (nums.get(mid) == target) index = mid;
                start = mid + 1;
            } else
                end = mid - 1;
        }
        return index;
    }                                                                                                                                                                                                                                                                                                                                                               
}   //  Main-class-end
```

**코드 (해시맵 풀이)**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;   //  수 개수
    private static int k;   //  두 수 합 k
    private static final List<Integer> nums = new ArrayList<Integer>();    //  수 목록
    private static final Map<Integer, Integer> map = new HashMap<>();      //  Key : 수, Value : 해당 수의 개수

    private static long ans = 0L;
    
    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        k = Integer.parseInt(tokens.nextToken());

        tokens = new StringTokenizer(br.readLine());
        for(int i = 0; i < n; i++)
            nums.add(Integer.parseInt(tokens.nextToken()));

        for(int i = 0; i < n; i++) {
            int num = nums.get(i);
            int target = k - num;

            //  이전 수 중에 target이 존재할 경우
            if(map.containsKey(target))
                ans += map.get(target);

            //  이전 수 중에 존재했던 num 개수 + 1
            map.put(num, map.getOrDefault(num, 0) + 1);
        }

        System.out.println(ans);
    }   //  main-end                                                                                                                                                                                                                                                                                                                           
}   //  Main-class-end
```