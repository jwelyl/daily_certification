# 25_09_24_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2035 증가수열

[2035번: 증가수열](http://boj.ma/2035/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Arrays;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static char[] input;
    private static int len;
    
    // minLastEnd[b] = 마지막 수의 시작 s (구간 [s..b]) , 없으면 -1
    private static int[] minLastEnd;

    public static void main(String[] args) throws Exception {
        input = br.readLine().toCharArray();
        len = input.length;

        minLastEnd = new int[len];
        Arrays.fill(minLastEnd, -1);

        for (int b = 0; b < len; b++) {
            int bestS = -1; // [bestS..b] 가 지금까지의 최소 마지막 수
            for (int s = 0; s <= b; s++) {
                boolean ok;
                if (s == 0) ok = true; // 첫 조각이면 이전 제약 없음
                else {
                    int prevEnd = s - 1;
                    int prevS = minLastEnd[prevEnd];
                    ok = (prevS != -1) && (compare(prevS, prevEnd, s, b) < 0); // prev < cur
                }
                if (!ok) 
                	continue;

                if (bestS == -1 || compare(s, b, bestS, b) < 0)
                    bestS = s; // 더 작은 마지막 수를 찾음
            }
            minLastEnd[b] = bestS; // b까지 가능하면 bestS가 설정됨
        }

        int s = minLastEnd[len - 1];
        // 출력: [s..n-1]의 숫자를 leading zero 제거 후 출력 (전부 0이면 0)
        int start = trimZeroStart(s, len - 1);
        if (start > len - 1)
            System.out.println("0");
        else
            System.out.println(new String(input, start, (len - 1) - start + 1));
    }	//	main-end

    //	[s1, e1] < [s2, e2]면 -1, [s1, e1] == [s2, e2]면 0, 그 외에는 1
    private static int compare(int s1, int e1, int s2, int e2) {
        int idx1 = trimZeroStart(s1, e1);
        int idx2 = trimZeroStart(s2, e2);	//	leading zero 제외하고 시작 index
        
        int len1 = Math.max(0, e1 - idx1 + 1);
        int len2 = Math.max(0, e2 - idx2 + 1);
        
        if (len1 != len2) 
        	return len1 < len2 ? -1 : 1;
        for (int i = 0; i < len1; i++) {
            char ch1 = input[idx1 + i];
            char ch2 = input[idx2 + i];
            
            if (ch1 != ch2) 
            	return ch1 < ch2 ? -1 : 1;
        }
        
        return 0;
    }

    //	[s, e] 중 leading zero 제외하고 시작하는 위치, 없으면 e + 1
    private static int trimZeroStart(int s, int e) {
        int idx = s;
        while (idx <= e && input[idx] == '0') 
        	idx++;
        
        return idx;
    }
}	//	Main-class-end
```