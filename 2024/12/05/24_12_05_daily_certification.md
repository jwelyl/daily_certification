# 24_12_05_daily_certification

# Problem Solving (Algorithm & SQL)

### LeetCode Design HashMap

[Design HashMap - LeetCode](https://leetcode.com/problems/design-hashmap/)

```java
class MyHashMap {
    private static final int NONE = -1;
    private int[] map;

    public MyHashMap() {
        map = new int[1_000_001];
        Arrays.fill(map, NONE);
    }
    
    public void put(int key, int value) {
        map[key] = value;
    }
    
    public int get(int key) {
        return map[key];
    }
    
    public void remove(int key) {
        map[key] = NONE;
    }
}

/**
 * Your MyHashMap object will be instantiated and called as such:
 * MyHashMap obj = new MyHashMap();
 * obj.put(key,value);
 * int param_2 = obj.get(key);
 * obj.remove(key);
 */
```

### LeetCode Longest Substring Without Repeating Characters

[Longest Substring Without Repeating Characters - LeetCode](https://leetcode.com/problems/longest-substring-without-repeating-characters/)

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if(s.length() == 0)
            return 0;

        int start = 0;
        int end = 0;
        int maxLength = 1;

        Set<Character> set = new HashSet<>();   //  [start, end]에 포함된 문자 집합

        set.add(s.charAt(end));

        while(true) {
            maxLength = Math.max(maxLength, end - start + 1);

            if(end + 1 < s.length()) {
                char ch = s.charAt(end + 1);

                if(set.contains(ch)) {  //  ch가 이미 포함될 경우
                    char out = s.charAt(start); //  제거할 문자
                    set.remove(out);
                    start++;
                }
                else {  //  ch를 추가할 수 있는 경우
                    set.add(ch);
                    end++;
                }
            }
            else break; //  끝에 도달한 경우
        }

        return maxLength;
    }
}
```

### LeetCode **Implement Trie (Prefix Tree)**

[Implement Trie (Prefix Tree) - LeetCode](https://leetcode.com/problems/implement-trie-prefix-tree/description/)

```java
class Trie {
    private TrieNode root;

    public Trie() {
        this.root = new TrieNode();
    }
    
    public void insert(String word) {
        TrieNode cur = this.root;

        for(int i = 0; i < word.length(); i++) {
            int idx = word.charAt(i) - 'a';

            if(cur.children[idx] == null) //  존재하지 않을 경우
                cur.children[idx] = new TrieNode();

            if(i == word.length() - 1)  //  마지막 노드일 경우
                cur.children[idx].isEnd = true;

            cur = cur.children[idx];
        }
    }
    
    public boolean search(String word) {
        TrieNode cur = this.root;

        for(int i = 0; i < word.length(); i++) {
            int idx = word.charAt(i) - 'a';

            if(cur.children[idx] == null)   //  i번째 문자에 해당하는 자식이 없을 경우
                return false;

            if(i == word.length() - 1 && !cur.children[idx].isEnd)  //  자식 노드가 마지막 노드여야 하는데 그렇지 않을 경우
                return false;

            cur = cur.children[idx];
        }

        return true;
    }
    
    public boolean startsWith(String prefix) {
        TrieNode cur = this.root;

        for(int i = 0; i < prefix.length(); i++) {
            int idx = prefix.charAt(i) - 'a';

            if(cur.children[idx] == null)   //  i번째 문자에 해당하는 자식이 없을 경우
                return false;

            cur = cur.children[idx];
        }

        return true;
    }

    static class TrieNode {
        boolean isEnd;          //  단어의 끝을 나타내는 노드
        TrieNode[] children;

        public TrieNode() {
            this.isEnd = false;
            this.children = new TrieNode[26];
        }
    }
}

/**
 * Your Trie object will be instantiated and called as such:
 * Trie obj = new Trie();
 * obj.insert(word);
 * boolean param_2 = obj.search(word);
 * boolean param_3 = obj.startsWith(prefix);
 */
```