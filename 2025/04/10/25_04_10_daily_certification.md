# 25_04_10_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 20164 홀수 홀릭 호석

[20164번: 홀수 홀릭 호석](http://boj.ma/20164/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	
    private static String N;
    private static int minCnt = Integer.MAX_VALUE;
    private static int maxCnt = Integer.MIN_VALUE;

    public static void main(String[] args) throws IOException {
        N = br.readLine();

        int cnt = countOdd(N);
        recurse(N, cnt);

        System.out.println(minCnt + " " + maxCnt);
    }	//	main-end

    private static int countOdd(String numStr) {
        int cnt = 0;
        for (int i = 0; i < numStr.length(); i++) {
            int digit = numStr.charAt(i) - '0';
            if (digit % 2 == 1) cnt++;
        }
        return cnt;
    }

    private static void recurse(String numStr, int cnt) {
        if (numStr.length() == 1) {
            minCnt = Math.min(minCnt, cnt);
            maxCnt = Math.max(maxCnt, cnt);
            return;
        }

        if (numStr.length() == 2) {
            int digit1 = numStr.charAt(0) - '0';
            int digit2 = numStr.charAt(1) - '0';
            String newStr = Integer.toString(digit1 + digit2);
            recurse(newStr, cnt + countOdd(newStr));
        } else {
            for (int i = 0; i < numStr.length() - 2; i++) {
                for (int j = i + 1; j < numStr.length() - 1; j++) {
                    int part1 = Integer.parseInt(numStr.substring(0, i + 1));
                    int part2 = Integer.parseInt(numStr.substring(i + 1, j + 1));
                    int part3 = Integer.parseInt(numStr.substring(j + 1));
                    String newStr = Integer.toString(part1 + part2 + part3);
                    recurse(newStr, cnt + countOdd(newStr));
                }
            }
        }
    }
}	//	Main-class-end
```