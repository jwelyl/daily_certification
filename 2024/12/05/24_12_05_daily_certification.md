# 24_12_05_daily_certification

## Process Synchronization

### Bounded-Buffer Problem (Producer-Consumer Problem)

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image.png)

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%201.png)

### Readers-Writers Problem

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%202.png)

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%203.png)

### Dining Philosophers Problem

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%204.png)

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%205.png)

두번째 해결 방법

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%206.png)

### Monitor

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%207.png)

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%208.png)

### Bounded-Buffer Problem (Producer-Consumer Problem)

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%209.png)

### Readers-Writers Problem

### Dining Philosophers Problem

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%2010.png)

## Deadlock

### Deadlock Conditions

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%2011.png)

### Deadlock Handling

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%2012.png)

### Deadlock Prevention

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%2013.png)

### Deadlock Avoidance

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%2014.png)

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%2015.png)

**Resource Allocation Graph Algorithm (자원 당 인스턴스가 하나인 경우)**

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%2016.png)

마지막 그림의 경우에는 데드락 발생 가능성이 조금이라도 있으므로 자원을 할당하지 않는다.

**Banker’s Algorithm (자원 당 인스턴스가 두개 이상인 경우)**

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%2017.png)

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%2018.png)

프로세스가 평생 사용할 최대 자원을 Max로 미리 계산해둔 상태

Need : Process가 최대로 요청할 수 있는 자원량

P0가 A 1개를 요청해도 주지 않음. Available만 보면 충분히 줄 수 있지만 Need가 Available보다 크므로 주지 않음 1개만 요청해서 사용하고 반납할지 Need를 모두 만족할때까지 반납하지 않을지 모르니까.

촤악의 경우를 상정함 (요청 프로세스가 최대 요청을 할 것이라고 가정하고 최대 요청을 받아들일 수 있을 경우에만 해당 프로세스의 요청을 받아들임)

P1, P3, P4, P2, P0의 요청 순서는 가능함

받아들일 수 있는 요청도 안받아들임. 딱 봐도 졸라게 비효율적임.

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%2019.png)

P4의 요청이 Available보다 크므로 요청을 수락할 수 없다.

P0의 요청 (0, 2, 0) 자체는 수락 가능하지만 P0의 최대 요청 (7, 4, 3)을 수락할 수 없으므로 요청을 수락하지 않는다. 준다고 Deadlock이 되는것은 아니지만 주지 않는다.

### Deadlock Detection and Recovery

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%2020.png)

자원 당 인스턴스가 1개일 경우

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%2021.png)

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%2022.png)

Cycle Detection Time Complexity

N Processes → O(N^2) (O(E) = O(N^2))

모든 Edge를 다 따라가봐야 하므로

자원 당 인스턴스가 2개 이상일 경우

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%2023.png)

Banker’s Algorithm과 다르게 Request는 현재 요청량임 (최대 요청량이 아님)

낙관적으로 보기

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%2024.png)

Recovery

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%2025.png)

Termination

- 데드락 걸린 프로세스 모두 죽이기
- 프로세스 하나씩 죽여보기 (데드락 풀릴때까지)

Preemption

- 데드락 걸린 프로세스 중 하나를 victim으로 선정하여 자원 강탈함
- 하나한테서만 계속 자원을 뺏지 않도록 고려해야함

### Deadlock Ignorance

![image.png](24_12_05_daily_certification%20152154b2a3b8803b978fe412c49bc1f9/image%2026.png)

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

### LeetCode **461. Hamming Distance**

[Hamming Distance - LeetCode](https://leetcode.com/problems/hamming-distance/description/)

```java
class Solution {
    public int hammingDistance(int x, int y) {
        int z = x ^ y;
        int ret = 0;

        while(z != 0) {
            int bit = z & 1;

            if(bit == 1)
                ret++;

            z >>= 1;
        }

        return ret;
    }
}
```