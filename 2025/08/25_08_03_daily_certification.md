# 25_08_03_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 3178 코코스

[3178번: 코코스](http://boj.ma/3178/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final int MAX = 26;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    단어 개수
    private static int K;    //    단어 길이 (전반부 K, 후반부 K)
    
    private static char[][] prefix;
    private static char[][] suffix;
    
    private static final Trie prefixTrie = new Trie();
    private static final Trie suffixTrie = new Trie();
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        prefix = new char[N][K];
        suffix = new char[N][K];
        
        for(int i = 0; i < N; i++) {
            String word = br.readLine();
        
            for(int j = 0; j < K; j++) {
                prefix[i][j] = word.charAt(j);
                suffix[i][j] = word.charAt(2 * K - 1 - j);
            }
            
            prefixTrie.insert(prefix[i]);
            suffixTrie.insert(suffix[i]);
        }
        
        System.out.println(prefixTrie.size + suffixTrie.size);
    }    //    main-end
    
    private static class TrieNode {
        private final TrieNode[] children = new TrieNode[MAX];
    }
    
    private static class Trie {
        private final TrieNode root = new TrieNode();
        private int size = 0;
        
        public void insert(char[] half) {
            TrieNode cur = this.root;
            
            for(char ch : half) {
                if(cur.children[ch - 'A'] == null) {
                    cur.children[ch - 'A'] = new TrieNode();
                    this.size++;
                }
                
                cur = cur.children[ch - 'A'];
            }
        }
    }
}    //    Main-class-end
```

### BOJ 25542 약속 장소

[25542번: 약속 장소](http://boj.ma/25542/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    단어 개수
    private static int L;    //    N개 단어 길이
    
    //    words[i] : i번째 단어
    //    words[0] : 비교 기준이 될 단어이자, 정답이 존재할 경우 정답이 될 단어
    private static char[][] words;
    
    private static int diffIdx = -1;
 
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        L = Integer.parseInt(st.nextToken());
        
        words = new char[N][];
        
        for(int i = 0; i < N; i++)
            words[i] = br.readLine().toCharArray();
        
        for(int idx = 0; idx < L; idx++) {
            //    diffs[i] : words[0]와 words[i]를 idx번째 문자를 제외하고 비교했을때 
            int[] diffs = new int[N];
            boolean find = true;    //    idx를 diffIdx로 쓸 수 있을 경우
            
            IDX_LOOP:
            for(int i = 1; i < N; i++) {
                for(int j = 0; j < L; j++) {
                    if(j == idx)    //    idx번째 문자는 비교 대상에서 제외
                        continue;
                    
                    if(words[0][j] != words[i][j]) {    //    j번째 문자가 서로 다를 경우
                        diffs[i]++;
                        if(diffs[i] > 1) {    //    idx번째 문자를 제외하고 봐도 서로 다른 문자가 2개 이상일 경우, idx를 바꿔보는 건 무의미함
                            find = false;
                            break IDX_LOOP;
                        }
                    }
                }
            }
            
            if(find) {
                diffIdx = idx;
                break;
            }
        }
        
        if(diffIdx != -1) {
            for(char ch = 'A'; ch <= 'Z'; ch++) {
                //    words[0][diffIdx]를 ch로 설정하고 다른 N - 1개 단어들과 비교했을 때
                //    모두 차이가 1개 미만일 경우 true
                boolean find = true;
                
                words[0][diffIdx] = ch;
                
                OUTER_LOOP:
                for(int i = 1; i < N; i++) {
                    int diff = 0;
                    
                    for(int j = 0; j < L; j++) {
                        if(words[0][j] != words[i][j]) {
                            diff++;
                            if(diff > 1) {    //    차이가 2개 이상일 경우
                                find = false;
                                break OUTER_LOOP;
                            }
                        }
                    }
                }
                
                if(find) {
                    System.out.println(new String(words[0]));
                    return;
                }
            }
        }

        //    정답을 찾지 못한 경우
        System.out.println("CALL FRIEND");
    }    //    main-end
}    //    Main-class-end
```