# 24_10_30_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2064 IP 주소

[](https://www.acmicpc.net/problem/2064)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;    //    IP 주소 개수
    private static char[][] ip;
    
    private static int idx = 0;
    private static final StringBuilder mask = new StringBuilder();
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        ip = new char[N][32];
        
        for(int i = 0; i < N; i++) {
        	st = new StringTokenizer(br.readLine(), ".");
        	for(int j = 0; j < 4; j++) {
        		String input = Integer.toBinaryString(Integer.parseInt(st.nextToken()));
        		String numStr = new StringBuilder("00000000").append(input).substring(input.length()).toString();
        		
        		for(int k = 0; k < 8; k++)
        			ip[i][j * 8 + k] = numStr.charAt(k);
        			
        	}
        }
        
        OUTER:
        while(idx < 32) {
        	char bit = ip[0][idx];
        	
        	for(int i = 1; i < N; i++) {
        		if(ip[i][idx] != bit)
        			break OUTER;
        	}
        
        	mask.append(1);
        	idx++;
        }
        
        while(idx < 32) {
        	mask.append(0);
        	idx++;
        }
        
        sb.append(getAddress()).append("\n").append(getMask());
        System.out.print(sb);
    }
    
    private static String getAddress() {
    	StringBuilder sb = new StringBuilder();
    	
    	for(int j = 0; j < 4; j++) {
    		sb.append(and(8 * j, mask.substring(8 * j, 8 * (j + 1))));
    		if(j != 3)
    			sb.append(".");
    	}
    	
    	return sb.toString();
    }
    
    private static int and(int start, String binary) {
    	int mult = 1 << 7;
    	int res = 0;
    	
    	for(int i = 0; i < 8; i++) {
    		if(ip[0][start + i] == '1' && binary.charAt(i) == '1')
    			res += mult;
    		mult >>= 1;
    	}
    	
    	return res;
    }
    
    private static int binaryToInt(String binary) {
    	int mult = 1;
    	int res = 0;
    	
    	for(int i = 7; i >= 0; i--) {
    		if(binary.charAt(i) == '1')
    			res += mult;
    		mult *= 2;
    	}
    	
    	return res;
    }
    
    private static String getMask() {
    	StringBuilder sb = new StringBuilder();
    	
    	for(int j = 0; j < 4; j++) {
    		sb.append(binaryToInt(mask.substring(j * 8, (j + 1) * 8)));
    		
    		if(j != 3)
    			sb.append(".");
    	}
    	
    	return sb.toString();
    }
}    //    Main-class-end
```