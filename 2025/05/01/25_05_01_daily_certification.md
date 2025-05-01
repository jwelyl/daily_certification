# 25_05_01_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 32178 용액 2

[32178번: 용액 2](http://boj.ma/32178/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static long[] prefixSum;
	private static final List<PrefixSum> psList = new ArrayList<>();
	
	private static long minAbs = Long.MAX_VALUE;
	private static long minPs = -1;
	private static int minStart = -1;
	private static int minEnd = -1;
	
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
       
        prefixSum = new long[N + 1];
        st = new StringTokenizer(br.readLine());
        
        psList.add(new PrefixSum(0, prefixSum[0]));
        for(int i = 1; i <= N; i++) {
        	prefixSum[i] = prefixSum[i - 1] + Integer.parseInt(st.nextToken());
        	psList.add(new PrefixSum(i, prefixSum[i]));
        }

        Collections.sort(psList);
        
        for(int i = 1; i <= N; i++) {
        	PrefixSum ps1 = psList.get(i - 1);
        	PrefixSum ps2 = psList.get(i);
        	int idx1 = ps1.idx;
        	int idx2 = ps2.idx;
        	long sum1 = ps1.sum;
        	long sum2 = ps2.sum;
        	
        	int start = -1;
        	int end = -1;
        	long abs = -1;
        	long ps = -1;
        	
        	if(idx1 < idx2) {
        		start = idx1 + 1;
        		end = idx2;
        		abs = Math.abs(sum2 - sum1);
        		ps = sum2 - sum1;
        	}
        	else {
        		start = idx2 + 1;
        		end = idx1;
        		abs = Math.abs(sum1 - sum2);
        		ps = sum1 - sum2;
        	}
        	
        	if(abs < minAbs) {
        		minAbs = abs;
        		minStart = start;
        		minEnd = end;
        		minPs = ps;
        	}
        }
        
        System.out.println(minPs);
        System.out.println(minStart + " " + minEnd);
    }	//	main-end
    
    private static class PrefixSum implements Comparable<PrefixSum> {
    	public int idx;
    	public long sum;
    	
    	public PrefixSum(int idx, long sum) {
    		this.idx = idx;
    		this.sum = sum;
    	}
    	
    	@Override
    	public int compareTo(PrefixSum other) {
    		return Long.compare(this.sum, other.sum);
    	}
    }
}	//	Main-class-end

```