# 24_05_02_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 특별한 문자**

[https://www.codetree.ai/missions/8/problems/special-character/description](https://www.codetree.ai/missions/8/problems/special-character/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/special-character/description)

**코드**

```java
import java.util.*;
import java.util.Map.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    private static final Map<Character, Integer> map = new LinkedHashMap<>();
    private static char ans = 0;
    private static String input = "";

    public static void main(String[] args) throws IOException {
        input = br.readLine();

        for(int i = 0; i < input.length(); i++) {
            char ch = input.charAt(i);
        
            map.put(ch, map.getOrDefault(ch, 0) + 1);
        }

        Set<Entry<Character, Integer>> entrySet = map.entrySet();

        for(Entry<Character, Integer> entry : entrySet) {
            char ch = entry.getKey();
            int cnt = entry.getValue();

            if(cnt == 1) {
                ans = ch;
                break;
            }
        }

        System.out.println(ans == 0 ? "None" : ans);
    }   //  main-end
}   //  Main-class-end
```

**CO{)E TREE 낮은 지점들**

[https://www.codetree.ai/missions/8/problems/lowest-points/description](https://www.codetree.ai/missions/8/problems/lowest-points/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/lowest-points/description)

**코드**

```java
import java.util.*;
import java.util.Map.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static final Map<Integer, Integer> map = new HashMap<>();   //  Key : x 좌표, Value : y 좌표
    
    private static int n = 0;       //  점 개수
    private static long ans = 0L;

    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());

        for(int i = 0; i < n; i++) {
            tokens = new StringTokenizer(br.readLine());
            int x = Integer.parseInt(tokens.nextToken());
            int y = Integer.parseInt(tokens.nextToken());

            if(!map.containsKey(x)) //  해당 x 좌표의 점이 없을 경우
                map.put(x, y);  //  해당 점을 넣음
            else {  //  해당 x 좌표의 점이 존재할 경우
                int prevY = map.get(x); //  존재하는 점의 y 좌표

                if(prevY > y)   //  새로 입력받은 y 좌표보다 클 경우
                    map.put(x, y);  //  더 작은 y 값으로 대체
            }
        }

        Set<Entry<Integer, Integer>> entrySet = map.entrySet();

        for(Entry<Integer, Integer> entry : entrySet)
            ans += entry.getValue();

        System.out.println(ans);
    }   //  main-end
}   //  Main-class-end
```

**CO{)E TREE 비율 구하기**

[https://www.codetree.ai/missions/8/problems/find-proportions/description](https://www.codetree.ai/missions/8/problems/find-proportions/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/find-proportions/description)

**코드**

```java
import java.util.*;
import java.util.Map.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    
    private static int n = 0;   //  단어 개수
    private static final Map<String, Integer> map = new TreeMap<>();

    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());

        for(int i = 0; i < n; i++) {
            String word = br.readLine();

            map.put(word, map.getOrDefault(word, 0) + 1);
        }

        for(Entry<String, Integer> entry : map.entrySet()) {
            String word = entry.getKey();
            int cnt = entry.getValue();
            double portion = 100.0 * cnt / n;

            sb.append(String.format("%s %.4f\n", word, portion));
        }

        System.out.print(sb);
    }   //  main-end
}   //  Main-class-end
```

**CO{)E TREE 처음 등장하는 위치**

[https://www.codetree.ai/missions/8/problems/first-appearing-position/description](https://www.codetree.ai/missions/8/problems/first-appearing-position/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/first-appearing-position/description)

**코드**

```java
import java.util.*;
import java.util.Map.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;
    private static final StringBuilder sb = new StringBuilder();
    
    private static int n = 0;   //  숫자 개수
    private static final Map<Integer, Integer> map = new TreeMap<>();

    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());

        tokens = new StringTokenizer(br.readLine());
        for(int i = 0; i < n; i++) {
            int num = Integer.parseInt(tokens.nextToken());
            
            if(!map.containsKey(num))
                map.put(num, i + 1);
        }

        for(Entry<Integer, Integer> entry : map.entrySet()) {
            int num = entry.getKey();
            int pos = entry.getValue();
            sb.append(num).append(" ").append(pos).append("\n");
        }

        System.out.print(sb);
    }   //  main-end
}   //  Main-class-end
```