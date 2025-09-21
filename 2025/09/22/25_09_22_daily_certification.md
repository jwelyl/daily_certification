# 25_09_22_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 11521 Boggle

[11521번: Boggle](http://boj.ma/11521/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.List;
import java.util.ArrayList;
import java.util.Collections;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();

    private static int W;              //    단어 개수
    private static final List<String> words = new ArrayList<>();     //    주어진 단어
    private static int D;              //    격자 크기
    private static char[][] board;     //    격자
    
    private static boolean find = false;
    
    public static void main(String[] args) throws IOException {
        W = Integer.parseInt(br.readLine());
        for(int w = 0; w < W; w++) {
            String word = br.readLine();
            boolean valid = true;    //    유효한 단어
            
            for(int i = 0; i < word.length(); i++) {
                char ch = word.charAt(i);
                
                if(ch == 'q') {
                    if(i == word.length() - 1 || word.charAt(i + 1) != 'u') {    //    'q'가 단독으로 나올 경우
                        valid = false;
                        break;
                    }    
                }
            }
            
            if(valid)
                words.add(word);
        }
        
        Collections.sort(words);
        
        while(true) {
            D = Integer.parseInt(br.readLine());
            
            if(D == 0) {
                System.out.print(sb);
                break;
            }
            
            board = new char[D][D];
            for(int d = 0; d < D; d++)
                board[d] = br.readLine().toCharArray();
            
            for(String word : words) {
                find = false;
                
                if(find(word))
                    sb.append(word).append("\n");
            }
            
            sb.append("-\n");
        }
    }    //    main-end
    
    private static boolean find(String word) {
        boolean[][] visited = new boolean[D][D];
        
        for(int y = 0; y < D; y++) {
            for(int x = 0; x < D; x++) {
                if(board[y][x] == word.charAt(0))
                    dfs(0, y, x, word, visited);
                
                if(find)
                    return true;
            }
        }
        
        return false;
    }
    
    private static void dfs(int idx, int y, int x, String word, boolean[][] visited) {
        char ch = word.charAt(idx);
        int nextIdx = ch == 'q' ? idx + 2 : idx + 1;
        visited[y][x] = true;
        
        if(nextIdx == word.length()) {
            find = true;
            return;
        }
        
        for(int d = 0; d < 8; d++) {
            int ny = y + dy[d];
            int nx = x + dx[d];
            
            if(isIn(ny, nx) && !visited[ny][nx] && board[ny][nx] == word.charAt(nextIdx))
                dfs(nextIdx, ny, nx, word, visited);
        }    
        
        visited[y][x] = false;
    }
    
    private static final int[] dy = {0, 1, 1, 1, 0, -1, -1, -1};
    private static final int[] dx = {1, 1, 0, -1, -1, -1, 0, 1};
    
    private static boolean isIn(int y, int x) {
        return (0 <= y && y < D) && (0 <= x && x < D);
    }
}    //    Main-class-end

```