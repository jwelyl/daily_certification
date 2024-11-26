# 24_11_27_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ** 17951 흩날리는 시험지 속에서 내 평점이 느껴진거야

[17951번: 흩날리는 시험지 속에서 내 평점이 느껴진거야](https://boj.ma/17951/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //   시험지 개수
    private static int K;    //   분할 수
    private static int[] cnts;    //    cnts[i] : i번째 시험지까지 맞은 문제 수 (누적합)
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        cnts = new int[N + 1];
        st = new StringTokenizer(br.readLine());
        for(int i = 1; i <= N; i++)
            cnts[i] = cnts[i - 1] + Integer.parseInt(st.nextToken());
        
        System.out.println(parametricSearch());
    }    //    main-end
    
    private static int parametricSearch() {
        int start = 0;
        int end = cnts[N];
        int res = 0;
        
        while(start <= end) {
            //    시험지들을 K개의 연속한 시험지들로 나눌때 맞춘 문제가 최소 mid가 되어야 함
            int mid = (start + end) / 2;
            
            if(ok(mid)) {    //    최소 mid가 되도록 분할 가능하면
                res = mid;            //    일단 mid 저장
                start = mid + 1;      //    더 큰 값 가능한지 확인해보기
            }
            else //    최소 mid가 되도록 분할 가능하지 않다면
                end = mid - 1;
        }
        
        return res;
    }
    
    //    맞은 문제가 최소 probs개가 될 수 있도록 K개의 구간으로 나눌 수 있으면 true
    private static boolean ok(int probs) {
        int cnt = 0;    //    구간 개수
        
        int start = 1;
        int end = 1;        //    구간 [start, end]
        
        while(end <= N) {
            int partSum = partialSum(start, end);    //    [start, end] 구간의 맞은 문제 수
            
            if(partSum >= probs) {    //    맞은 문제수가 probs 이상인 구간이 하나 생겼을 경우
                cnt++;    //    구간 개수 1 증가
                
                if(cnt == K)    //    구간을 더 나눌 필요가 없을 경우
                    break;
                
                start = end + 1;
                end = start;        //    다음 구간 찾기
            }
            else    //    [start, end] 구간 만으로는 맞은 문제수가 probs보다 부족할 경우
                end++;
        }
        
        return cnt == K;    //    K개의 구간으로 나눌 수 있으면 true
    }
    
    private static int partialSum(int from, int to) {
        return cnts[to] - cnts[from - 1];
    }
}    //    Main-class-end
```

### **BOJ 3967**  매직 스타

[3967번: 매직 스타](https://boj.ma/3967/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.List;
import java.util.ArrayList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    
    private static final List<int[]> posList = new ArrayList<>();
    private static final char[][] board = new char[5][9];
    private static final boolean[] used = new boolean[13];
    private static boolean find = false;
    
    public static void main(String[] args) throws IOException {
        for(int r = 0; r < 5; r++) {
            board[r] = br.readLine().toCharArray();
            for(int c = 0; c < 9; c++) {
                if(board[r][c] == 'x')
                    posList.add(new int[] {r, c});
                else if('A' <= board[r][c] && board[r][c] <= 'L')
                    used[board[r][c] - 'A' + 1] = true;
            }  
        }
        
        bruteforcing(0);
    }    //    main-end
    
    private static void bruteforcing(int nth) {
        if(find)
            return;
        
        if(nth == posList.size()) {    //    모든 칸을 다 채웠을 경우
            if(ok()) {
                find = true;
                
                for(int r = 0; r < 5; r++)
                    sb.append(new String(board[r])).append("\n");
                
                System.out.print(sb);
            }
            
            return;
        }
        
        int[] pos = posList.get(nth);
        int r = pos[0];
        int c = pos[1];
        
        for(int i = 1; i <= 12; i++) {
            if(!used[i]) {
                used[i] = true;
                board[r][c] = (char)('A' + i - 1);
                
                bruteforcing(nth + 1);
                
                used[i] = false;
            }
        }
    }
    
    private static boolean ok() {
        if(board[0][4] + board[1][3] + board[2][2] + board[3][1] - 4 * 'A' + 4 != 26)
            return false;
        if(board[0][4] + board[1][5] + board[2][6] + board[3][7] - 4 * 'A' + 4 != 26)
            return false;
        if(board[1][1] + board[1][3] + board[1][5] + board[1][7] - 4 * 'A' + 4 != 26)
            return false;
        if(board[3][1] + board[3][3] + board[3][5] + board[3][7] - 4 * 'A' + 4 != 26)
            return false;
        if(board[4][4] + board[3][3] + board[2][2] + board[1][1] - 4 * 'A' + 4 != 26)
            return false;
        if(board[4][4] + board[3][5] + board[2][6] + board[1][7] - 4 * 'A' + 4 != 26)
            return false;
        
        return true;
    }
}    //    Main-class-end
```

### **조건에 맞는 개발자 찾기**

[](https://school.programmers.co.kr/learn/courses/30/lessons/276034)

```sql
SELECT ID, EMAIL, FIRST_NAME, LAST_NAME
FROM DEVELOPERS
WHERE SKILL_CODE & (SELECT CODE FROM SKILLCODES WHERE NAME = 'Python') or SKILL_CODE & (SELECT CODE FROM SKILLCODES WHERE NAME = 'C#') 
ORDER BY ID;
```

### 업그레이드 된 아이템 구하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/273711)

```sql
SELECT B.ITEM_ID, A.ITEM_NAME, A.RARITY
FROM ITEM_INFO AS A
INNER JOIN ITEM_TREE AS B
ON A.ITEM_ID = B.ITEM_ID
WHERE B.PARENT_ITEM_ID IN
(SELECT ITEM_ID
FROM ITEM_INFO
WHERE RARITY = 'RARE')
ORDER BY B.ITEM_ID DESC;
```