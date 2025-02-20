# 25_02_20_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 14905 소수 4개의 합

[14905번: 소수 4개의 합](http://boj.ma/14905/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    
    public static void main(String[] args) throws IOException {
    	while(true) {
    		String input = br.readLine();
    		
    		if(input == null) {
    			System.out.print(sb);
    			break;
    		}
    		
    		int num = Integer.parseInt(input);
    		
    		if(num < 8) {
    			sb.append("Impossible.\n");
    			continue;
    		}
    		else if(num % 2 == 0) {
    			sb.append("2 2 ");
    			num -= 4;
    		}
    		else {
    			sb.append("2 3 ");
    			num -= 5;
    		}
    		
    		for(int d1 = num / 2; d1 >= 2; d1--) {
    			int d2 = num - d1;
    			
    			if(isPrime(d1) && isPrime(d2)) {
    				sb.append(d1).append(" ").append(d2).append("\n");
    				break;
    			}
    		}
    	}
    }    //    main-end
    
    private static boolean isPrime(int num) {
    	if(num == 1)
    		return false;
    	
    	if(num == 2)
    		return true;
    	
    	for(int d = 2; d * d <= num; d++) {
    		if(num % d == 0)
    			return false;
    	}
    	
    	return true;
    }
}    //    Main-class-end
```