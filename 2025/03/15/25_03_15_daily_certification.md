# 25_03_15_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 25332 수들의 합 8

[25332번: 수들의 합 8](http://boj.ma/25332/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.Map;
import java.util.Map.Entry;
import java.util.HashMap;
import java.util.Set;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    //    Key : prefixSumB[i] - prefixSumA[i], Value : Key 값 가지는 누적합 차 개수
    private static final Map<Integer, Integer> map = new HashMap<>();
    
    private static int[] prefixSumA;
    private static int[] prefixSumB;
    
    private static long answer = 0;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        prefixSumA = new int[N + 1];
        prefixSumB = new int[N + 1];
        
        st = new StringTokenizer(br.readLine());
        for(int i = 1; i <= N; i++)
            prefixSumA[i] = prefixSumA[i - 1] + Integer.parseInt(st.nextToken());
        st = new StringTokenizer(br.readLine());
        for(int i = 1; i <= N; i++)
            prefixSumB[i] = prefixSumB[i - 1] + Integer.parseInt(st.nextToken());
        
        for(int i = 0; i <= N; i++) {
            int diff = prefixSumB[i] - prefixSumA[i];
            map.put(diff, map.getOrDefault(diff, 0) + 1);
        }
        
        for(int cnt : map.values())
            answer += (long)cnt * (cnt - 1) / 2;
        
        System.out.println(answer);
    }    //    main-end
}    //    Main-class-end

```

### 프로그래머스 멸종위기의 대장균 찾기

[](https://school.programmers.co.kr/learn/courses/30/lessons/301651)

```sql
-- 코드를 작성해주세요
with recursive gt as (
    --  anchor query (한번만 실행)
    select id, 1 as generation
    from ecoli_data
    where parent_id is null --  최초 개체 (1세대) : 이 결과가 gt에 최초 저장됨

    union all   -- anchor query와 recursive 쿼리 연결

    -- recursive query
    select e.id, gt.generation + 1
    from ecoli_data e inner join gt
    on e.parent_id = gt.id
)

select count(gt.id) count, gt.generation
from gt left join ecoli_data e
on e.parent_id = gt.id
where e.id is null
group by gt.generation
order by gt.generation;
```