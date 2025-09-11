# 25_09_11_daily_certification

# Problem Solving (Algorithm & SQL)

### 프로그래머스 가사 검색

[](https://school.programmers.co.kr/learn/courses/30/lessons/60060#)

```java
import java.util.Map;
import java.util.HashMap;

class Solution {
    public int[] solution(String[] words, String[] queries) {
        int[] answer = new int[queries.length];
        
        Trie prefixTrie = new Trie();
        Trie suffixTrie = new Trie();
        
        for(int i = 0; i < words.length; i++) {
            prefixTrie.insertWord(words[i]);
            suffixTrie.insertWord(new StringBuilder(words[i]).reverse().toString());
        }
        
        prefixTrie.dfs();
        suffixTrie.dfs();
        
        for(int i = 0; i < queries.length; i++) {
            String word = queries[i];
            
            if(word.charAt(0) == '?')  //  접미사 쿼리일 경우
                answer[i] = suffixTrie.find(new StringBuilder(word).reverse().toString());
            else
                answer[i] = prefixTrie.find(word);
        }
        
        return answer;
    }
}

class TrieNode {
    public final char ch;
    public final Map<Character, TrieNode> children;
    public final Map<Integer, Integer> lengthMap;
    public boolean end;
    
    public TrieNode(char ch) {
        this.ch = ch;
        this.children = new HashMap<>();
        this.lengthMap = new HashMap<>();
        this.end = false;
    }
}

class Trie {
    public final TrieNode root = new TrieNode(' ');
    
    public void insertWord(String word) {
        TrieNode cur = root;
        
        for(int i = 0; i < word.length(); i++) {
            char ch = word.charAt(i);
            TrieNode child = cur.children.getOrDefault(ch, new TrieNode(ch));
            
            if(i == word.length() - 1)
                child.end = true;
            
            cur.children.put(ch, child);
            cur = child;
        }
    }
    
    public void dfs() {
        this.dfs(this.root);
    }
    
    public int find(String word) {
        int cnt = 0;    //  '?' 개수
        
        for(int i = word.length() - 1; i >= 0; i--) {
            if(word.charAt(i) != '?')
                break;
            
            cnt++;
        }
        
        TrieNode cur = this.root;
        
        for(int i = 0; i < word.length() - cnt; i++) {
            char ch = word.charAt(i);
            cur = cur.children.get(ch);
            
            if(cur == null)
                return 0;
        }
        
        return cur.lengthMap.getOrDefault(cnt, 0);
    }
    
    private void dfs(TrieNode node) {
        for(char key : node.children.keySet()) {
            TrieNode child = node.children.get(key);
            
            dfs(child);
            
            if(child.end)
                node.lengthMap.put(1, node.lengthMap.getOrDefault(1, 0) + 1);
            
            for(int length : child.lengthMap.keySet()) {
                int value = child.lengthMap.get(length);
                node.lengthMap.put(length + 1, node.lengthMap.getOrDefault(length + 1, 0) + value);
            }
        }
    }
}
```

### 조건에 맞는 사용자 정보 조회하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/164670)

```sql
select 
    t2.user_id, 
    t2.nickname, 
    concat(t2.city, ' ', t2.street_address1, ' ', t2.street_address2) as '전체주소', 
    concat(substr(t2.tlno from 1 for 3), '-', substr(t2.tlno from 4 for 4), '-', substr(t2.tlno from 8)) as '전화번호'
from 
    used_goods_board t1 inner join used_goods_user t2
on t1.writer_id = t2.user_id
group by t2.user_id
having count(t2.user_id) >= 3
order by t2.user_id desc;
```