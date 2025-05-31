# 25_05_31_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 26077 서커스 나이트

[26077번: 서커스 나이트](http://boj.ma/26077/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
    private static final int MAX = 1_000_000;

    private static final int[] PARENTS = new int[MAX + 1];
    private static final int[] DS_SIZES = new int[MAX + 1];
    private static final int[] SPF = new int[MAX + 1]; // smallest prime factor
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int[] ids;
  
    private static int answer = 0;

    public static void main(String[] args) throws IOException {
    	init();
    	
        N = Integer.parseInt(br.readLine());
        ids = new int[N];

        st = new StringTokenizer(br.readLine());
        for (int i = 0; i < N; i++)
            ids[i] = Integer.parseInt(st.nextToken());

        for (int id : ids) {
        	List<Integer> primes = getPrimeFactors(id);
            for (int prime : primes)
                union(id, prime);
        }
        
        for (int id : ids) {
            int root = find(id);
            DS_SIZES[root]++;
            answer = Math.max(answer, DS_SIZES[root]);
        }

        System.out.println(answer);
    }	//	main-end

    private static void init() {
    	for (int num = 0; num <= MAX; num++) {
            PARENTS[num] = num;
        }
    	
    	for(int num = 2; num * num <= MAX; num++) {
			if(SPF[num] == 0) {
				SPF[num] = num;
				
				for(int mult = num * num; mult <= MAX; mult += num) {
					if(SPF[mult] == 0)
						SPF[mult] = num;
				}
			}
		}
		
		for(int num = 2; num <= MAX; num++) {
			if(SPF[num] == 0)
				SPF[num] = num;
		}
    }

    private static List<Integer> getPrimeFactors(int num) {
        Set<Integer> factors = new HashSet<>();
        while (num > 1) {
            int prime = SPF[num];
            factors.add(prime);
            while (num % prime == 0) num /= prime;
        }
        return new ArrayList<>(factors);
    }

    private static int find(int v) {
    	return v == PARENTS[v] ? v : (PARENTS[v] = find(PARENTS[v]));
    }

    private static void union(int v1, int v2) {
        v1 = find(v1);
        v2 = find(v2);

        if (v1 != v2)
            PARENTS[v2] = v1;
    }
}	//	Main-class-end
```