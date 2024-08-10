# 24_08_10_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 17255 **N으로 만들기**

[](https://www.acmicpc.net/problem/17255)

```java
import java.io.*;
import java.util.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static char[] N;
    private static int len;
    private static final Set<String> set = new HashSet<>();
    
    private static int ans = 0;
    
    public static void main(String[] args) throws IOException {
        N = br.readLine().toCharArray();
        len = N.length;
        
        for(int i = 0; i < len; i++)
            dfs(i, i, "" + N[i], "" + N[i]);
        
        System.out.println(set.size());
    }    //    main-end
    
    private static void dfs(int start, int end, String cnum, String cpath) {
        if(end - start + 1 == len) {    //    주어진 문자열 N이 완성된 경우
            if(!set.contains(cpath)) {
                set.add(cpath);
                ans++;
            }
            
            return;
        }
        
        String nnum = cnum;
        String npath = cpath;
        if(start - 1 >= 0) {    //    왼쪽으로 붙일 수 있는 경우
            nnum = N[start - 1] + cnum;
            npath = cpath + "@" + nnum;
            
            dfs(start - 1, end, nnum, npath);
        }
        if(end + 1 < len) {    //    오른쪽으로 붙일 수 있는 경우
            nnum = cnum + N[end + 1];
            npath = cpath + "@" + nnum;
            
            dfs(start, end + 1, nnum, npath);
        }
    }
}    //    Main-class-end
```