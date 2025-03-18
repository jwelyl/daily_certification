# 25_03_18_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 15554 전시회

[](https://www.acmicpc.net/problem/15554)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;

    private static Artwork[] artworks;
    private static long[] prefixSum;	//	prefixSum[i] : 1 ~ i번째 미술품의 가치 총합
    
    private static long[] min;			//	min[i] : 1 <= k <= i일때 prefixSum[k- 1] - artworks[k].size의 최솟값
    
    private static long answer = 0;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
 
        artworks = new Artwork[N + 1];
        artworks[0] = new Artwork(0, 0);
        
        prefixSum = new long[N + 1];
        min = new long[N + 1];
        min[0] = Long.MAX_VALUE;
        
        for(int i = 1; i <= N; i++) {
        	st = new StringTokenizer(br.readLine());
        	artworks[i] = new Artwork(Long.parseLong(st.nextToken()), Integer.parseInt(st.nextToken()));
        }
        
        Arrays.sort(artworks);
        for(int i = 1; i <= N; i++) {
        	prefixSum[i] = prefixSum[i - 1] + artworks[i].value;
        	min[i] = Math.min(min[i - 1], prefixSum[i - 1] - artworks[i].size);
        }
        
        for(int i = 1; i <= N; i++) {
        	long res = prefixSum[i] - artworks[i].size - min[i];
        	answer = Math.max(answer, res);
        }
        
        System.out.println(answer);
    }    //    main-end
    
    private static class Artwork implements Comparable<Artwork> {
    	public long size;
    	public int value;
    	
    	public Artwork(long size, int value) {
    		this.size = size;
    		this.value = value;
    	}

		@Override
		public int compareTo(Artwork other) {
			int res = Long.compare(this.size, other.size);	//	크기 작은 순으로 정렬
			
			if(res == 0)	//	크기가 같을 경우
				res = Integer.compare(this.value, other.value);	//	가치 작은 순으로 정렬
			
			return res;
		}
    }
}    //    Main-class-end
```

### 프로그래머스 특정 조건을 만족하는 물고기별 수와 최대 길이 구하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/298519)

```sql
-- 코드를 작성해주세요
select count(t.id) fish_count, max(t.fish_length) max_length, t.fish_type
from
(select id, fish_type, (
                           case 
                             when length is null or length <= 10 
                             then 10 
                             else length
                           end
                      ) fish_length
from fish_info) t
group by t.fish_type
having avg(t.fish_length) >= 33
order by t.fish_type;
```