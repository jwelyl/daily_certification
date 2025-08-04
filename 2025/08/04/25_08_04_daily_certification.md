# 25_08_04_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 14229 DNA 문자열

[14229번: DNA 문자열](http://boj.ma/14229/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.Queue;
import java.util.LinkedList;

public class Main {
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static char[] dna;
    private static final char[] BASE = {'A', 'C', 'G', 'T'};
    
    private static final Trie trie = new Trie();
    
    public static void main(String[] args) throws IOException {
        dna = br.readLine().toCharArray();
        
        //	dna 문자열의 길이가 최대 2,000임
        //	A, C, G, T로 이루어진 길이 5인 문자열의 경우의 수는 4^5 = 1,024
        //  최대 길이 2,000인 문자열이 가질 수 있는 길이 5인 부분문자열 개수는 2000 - 5 + 1 = 1,996개로 가능한 1,024개보다 많음
        //	A, C, G, T로 이루어진 길이 6인 문자열의 경우의 수는 4^6 = 4,096
        //  최대 길이 2,000인 문자열이 가질 수 있는 길이 6인 부분문자열 개수는 2000 - 6 + 1 = 1,995개로 가능한 4,096개보다 적음
        //  즉 A, C, G, T로 이루어진 길이 6인 문자열 중 dna 문자열에 포함되지 않는 문자열이 반드시 존재
        for(int len = 1; len <= Math.min(6, dna.length); len++) {
            for(int fromIdx = 0; fromIdx <= dna.length - len; fromIdx++)
                trie.insert(dna, fromIdx, len);
        }
        
        System.out.println(trie.getDna());
    }    //    main-end
    
    private static class TrieNode {
        private final TrieNode[] children = new TrieNode[4];
        private TrieNode parent;
        private char base;
        
        public TrieNode(char base) {
            this.base = base;
        }
    }
    
    private static class Trie {
        private final TrieNode root = new TrieNode('X');
            
        public Trie() {
            this.root.parent = this.root;
        }
        
        public void insert(char[] dna, int fromIdx, int len) {
            TrieNode cur = this.root;
            
            for(int i = fromIdx; i < fromIdx + len; i++) {
                char base = dna[i];
                int idx = base == 'A' ? 0 : (base == 'C' ? 1 : (base == 'G' ? 2 : 3));
                
                if(cur.children[idx] == null) {
                    cur.children[idx] = new TrieNode(base);
                    cur.children[idx].parent = cur;
                }
                
                cur = cur.children[idx];
            }
        }
        
        //    A, C, G, T로 이루어진 DNA 문자열 중 dna에 포함되지 않은 최소 길이 문자열 반환
        public String getDna() {
            Queue<TrieNode> queue = new LinkedList<>();
            TrieNode now = null;
            StringBuilder res = new StringBuilder();
            
            queue.offer(this.root);
            
            FIND_LOOP:
            while(!queue.isEmpty()) {
                TrieNode cur = queue.poll();
                
                for(int i = 0; i < 4; i++) {
                    TrieNode next = cur.children[i];
                    
                    if(next == null) {
                        next = new TrieNode(BASE[i]);
                        next.parent = cur;
                        now = next;
                        break FIND_LOOP;
                    }
                    
                    queue.offer(next);
                }
            }
        
            while(now != root) {
                res.append(now.base);
                now = now.parent;
            }
            
            return res.reverse().toString();
        }
    }
}    //    Main-class-end
```

### BOJ 10888 두 섬간의 이동

[10888번: 두 섬간의 이동](http://boj.ma/10888/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    
    private static int N;
    
    private static int[] parents;
    private static int[] dsSizes;
    
    private static long answer1 = 0;
    private static long answer2 = 0;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        parents = new int[N + 1];
        dsSizes = new int[N + 1];
        
        for(int v = 1; v <= N; v++) {
            parents[v] = v;
            dsSizes[v] = 1;
        }
        
        for(int i = 0; i < N - 1; i++) {
            int v = Integer.parseInt(br.readLine());
            union(v, v + 1);
            sb.append(answer1).append(" ").append(answer2).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static int find(int v) {
        return v == parents[v] ? v : (parents[v] = find(parents[v]));
    }
    
    private static void union(int v1, int v2) {
        v1 = find(v1);
        v2 = find(v2);
        
        parents[v2] = v1;
        answer1 -= comb2(dsSizes[v1]);
        answer1 -= comb2(dsSizes[v2]);
        answer2 -= distSum(dsSizes[v1]);
        answer2 -= distSum(dsSizes[v2]);

        dsSizes[v1] += dsSizes[v2];
        dsSizes[v2] = 0;
        answer1 += comb2(dsSizes[v1]);
        answer2 += distSum(dsSizes[v1]);
    }
    
    //    cnt개 중에 2개 고르는 경우의 수
    private static long comb2(int cnt) {
        return (long)cnt * (cnt - 1) / 2; 
    }
    
    //    정점 cnt개가 일렬로 있을 때 그 중 임의의 두 정점을 골랐을때 거리의 총합
    private static long distSum(int cnt) {
        return ((long)cnt * cnt * cnt - cnt) / 6;
    }
}    //    Main-class-end
```