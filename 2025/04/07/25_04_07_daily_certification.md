# 25_04_07_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 24891 단어 마방진

[24891번: 단어 마방진](http://boj.ma/24891/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;

    private static int L = 0;
    private static int N = 0;
    
    private static String[] words;
    private static String ansStr = null;
    private static char[][] ansSquare = null;

    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        L = Integer.parseInt(st.nextToken());
        N = Integer.parseInt(st.nextToken());
        
        words = new String[N];
        for(int i = 0; i < N; i++)
            words[i] = br.readLine();
        
        Arrays.sort(words);  // 사전순으로 미리 정렬
        
        permutation(0, new int[L], new boolean[N]);
        
        if (ansStr == null) {
            System.out.println("NONE");
        } else {
            for (int i = 0; i < L; i++) {
                for (int j = 0; j < L; j++) {
                    sb.append(ansSquare[i][j]);
                }
                sb.append("\n");
            }
            System.out.print(sb);
        }
    }
    
    private static void permutation(int nth, int[] selected, boolean[] used) {
        if (nth == L) {
            char[][] square = new char[L][L];
            StringBuilder str = new StringBuilder();
            
            for (int i = 0; i < L; i++) {
                square[i] = words[selected[i]].toCharArray();
                str.append(words[selected[i]]);
            }
            
            if (isMagicSquare(square)) {
                if (ansStr == null || str.toString().compareTo(ansStr) < 0) {
                    ansStr = str.toString();
                    ansSquare = square;
                }
            }
            return;
        }
        
        for (int i = 0; i < N; i++) {
            if (!used[i]) {
                used[i] = true;
                selected[nth] = i;
                permutation(nth + 1, selected, used);
                used[i] = false;
            }
        }
    }

    // 행과 열을 동시에 비교하여 불필요한 반복을 줄임
    private static boolean isMagicSquare(char[][] square) {
        for (int i = 0; i < L; i++) {
            for (int j = 0; j < L; j++) {
                if (square[i][j] != square[j][i]) {
                    return false;
                }
            }
        }
        return true;
    }
}
```