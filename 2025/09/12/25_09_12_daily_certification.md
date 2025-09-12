# 25_09_12_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 6493 Slurpys

[6493번: Slurpys](http://boj.ma/6493/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.regex.Pattern;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    
    private static int T;
    
    public static void main(String[] args) throws IOException {
    	T = Integer.parseInt(br.readLine());
    	
    	for(int tc = 1; tc <= T; tc++) {
    		String input = br.readLine();
    		boolean isSluppy = false;
    		
    		if(input.length() >= 5) {
    			for(int i = 2; i <= input.length() - 3; i++) {
    				if(isSlimp(input.substring(0, i)) && isSlump(input.substring(i))) {
    					isSluppy = true;
    					break;
    				}
    			}
    		}
    		
    		sb.append(isSluppy ? "YES\n" : "NO\n");
    	}
    	
    	System.out.print(sb);
    }    //    main-end
    
    private static boolean isSlimp(String str) {
    	int len = str.length();
    	
    	if(len >= 2) {
        	if(str.charAt(0) != 'A')
        		return false;
    		
    		if(len == 2)
    			return str.charAt(1) == 'H';
    	
    		if(str.charAt(len - 1) != 'C')
    			return false;
    		
    		return (str.charAt(1) == 'B' && isSlimp(str.substring(2, len - 1))) || isSlump(str.substring(1, len - 1)); 
    	}
    	
    	return false;
    }
    
    private static boolean isSlump(String str) {
    	Pattern slump = Pattern.compile("((D|E)F+)+G");
    	return slump.matcher(str).matches();
    }
}    //    Main-class-end
```