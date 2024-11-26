# 24_11_26_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ** 16472 고냥이

[16472번: 고냥이](https://boj.ma/16472/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static int N;          //    포함 가능한 문자 종류 수
    private static char[] text;    //    주어진 문자열
    
    private static int start = 0;
    private static int end = 0;        //    두 포인터
    
    //    [start, end] 부분 문자열에 포함된 'a' ~ 'z'까지 개수
    private static final int[] cnts = new int[26];
    //    [start, end] 부분 문자열에 포함된 문자 종류 수
    private static int kind = 0;
    
    private static int maxLen = 0;    //    최대 구간 길이
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        text = br.readLine().toCharArray();
        end = Math.min(N - 1, text.length - 1);
        
        maxLen = Math.min(N, text.length);
        
        //    최초 구간 [start, end] 처리
        for(int i = start; i <= end; i++) {
            char ch = text[i];
            
            if(cnts[ch - 'a'] == 0)    //    이전에 존재하지 않던 문자인 경우
                kind++;    //    문자 종류 1 증가
            
            cnts[ch - 'a']++;    //    ch가 포함된 개수 증가
        }

        while(true) {
            if(end + 1 == text.length)    //    끝까지 왔을 경우
                break;
            
            char ch = text[end + 1];

            //    [start, end] 구간에 ch가 포함되지 않았는데, 이미 N 종류의 문자가 있을 경우, ch를 추가할 수 없음
            if(cnts[ch - 'a'] == 0 && kind == N) {
                char out = text[start++];    //    start가 가리키던 문자
                cnts[out - 'a']--;    //    start가 가리키던 문자 제외
                
                if(cnts[out - 'a'] == 0)    //    out이 더 이상 없을 경우
                    kind--;    //    문자 종류 1 감소
            }
            //    ch를 추가할 수 있을 경우
            else {
                maxLen = Math.max(maxLen, end - start + 2);    //    최대 구간 길이 갱신
                cnts[ch - 'a']++;    //    해당 문자 추가
                
                if(cnts[ch - 'a'] == 1)    //    새로 추가된 문자일 경우
                    kind++;
                
                end++;
            }
        }
        
        System.out.println(maxLen);
    }    //    main-end
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