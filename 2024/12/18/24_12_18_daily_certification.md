# 24_12_18_daily_certification

```
[#353 koreii] 데일리인증 20241218
1. Spring DB
1-1. Transaction
	- TransactionManager / TransactionSynchronizationManager
	- ThreadLocal
	- DataSourceUtils
	- TransactionTemplate
1-2. Transaction AOP
	- @Transactional
1-3. Spring Boot AutoConfiguration
2. Java Exception
	- Java Exception Hierarchy
	- Checked Exception
	- Unchecked Exception
3. 코딩 테스트 대비 알고리즘 학습
- Stack, Queue (LeetCode 225. Implement Stack using Queues)
- DFS(Tree Diameter), Topological Sort (LeetCode 310. Minimum Height Trees)
```

### LeetCode Implement Stack using Queues

[Implement Stack using Queues - LeetCode](https://leetcode.com/problems/implement-stack-using-queues/description/)

```java
class MyStack {
    private Queue<Integer> queue1;
    private Queue<Integer> queue2;

    public MyStack() {
        queue1 = new LinkedList<>();
        queue2 = new LinkedList<>();
    }
    
    public void push(int x) {
        queue1.offer(x);
    }
    
    public int pop() {
        while(queue1.size() > 1)
            queue2.offer(queue1.poll());
        
        int res = queue1.poll();
        Queue<Integer> tmp = queue1;
        queue1 = queue2;
        queue2 = tmp;

        return res;
    }
    
    public int top() {
        while(queue1.size() > 1)
            queue2.offer(queue1.poll());
        
        int res = queue1.poll();
        queue2.offer(res);

        Queue<Integer> tmp = queue1;
        queue1 = queue2;
        queue2 = tmp;

        return res;
    }
    
    public boolean empty() {
        return queue1.isEmpty();
    }
}

/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack obj = new MyStack();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.top();
 * boolean param_4 = obj.empty();
 */
```

### LeetCode Minimum Height Trees

[Implement Stack using Queues - LeetCode](https://leetcode.com/problems/implement-stack-using-queues/description/)

**DFS**

```java
class Solution {
    private List<Integer>[] tree;   //  트리
    private int root = 0;       //  루트
    private int farthest = 0;       //  루트로부터 가장 먼 정점
    private int farthestDist = 0;   //  루트로부터 가장 먼 정점까지의 거리, 촤종적으로 트리의 지름
    private boolean[] visited;
    private int[] dist1;
    private int[] dist2;
    private List<Integer> answer = new ArrayList<>();
    
    public List<Integer> findMinHeightTrees(int n, int[][] edges) {
        tree = new ArrayList[n];
        for(int v = 0; v < n; v++)
            tree[v] = new ArrayList<>();

        visited = new boolean[n];
        dist1 = new int[n];
        dist2 = new int[n];

        for(int e = 0; e < edges.length; e++) {
            int v1 = edges[e][0];
            int v2 = edges[e][1];

            tree[v1].add(v2);
            tree[v2].add(v1);
        }

        dfs(root, 0, dist1);    //  root로부터 가장 먼 정점 찾기
        root = farthest;        //  기존 root에서 가장 먼 점을 다시 루트로 함 (지름의 한쪽 끝 점)
        farthestDist = 0;
        Arrays.fill(dist1, 0);
        Arrays.fill(visited, false);
        dfs(root, 0, dist2);    //  지름의 다른 한쪽 끝 점 찾기
        Arrays.fill(visited, false);
        dfs(farthest, 0, dist1);

        for(int v = 0; v < n; v++) {
            if(dist1[v] + dist2[v] == farthestDist && Math.abs(dist1[v] - dist2[v]) <= 1)
                answer.add(v);
        }

        return answer;
    }

    private void dfs(int v, int d, int[] dist) {
        visited[v] = true;
        dist[v] = d;

        if(dist[v] > farthestDist) {
            farthestDist = dist[v];
            farthest = v;
        }

        for(int next : tree[v]) {
            if(!visited[next])
                dfs(next, d + 1, dist);
        }
    }
}
```

**Topological Sort**

```java
class Solution {
    public List<Integer> findMinHeightTrees(int n, int[][] edges) {
        if (n == 1) 
            return Collections.singletonList(0); // 노드가 하나일 때 예외 처리
        
        int[] indegrees = new int[n];
        List<Integer>[] tree = new ArrayList[n];

        for (int v = 0; v < n; v++)
            tree[v] = new ArrayList<>();

        for (int e = 0; e < edges.length; e++) {
            int v1 = edges[e][0];
            int v2 = edges[e][1];

            indegrees[v1]++;
            indegrees[v2]++;
            
            tree[v1].add(v2);  // 간선 추가
            tree[v2].add(v1);  // 양방향 간선
        }

        Queue<Integer> queue = new LinkedList<>();
        List<Integer> answer = new ArrayList<>();

        // 초기 리프 노드 추가
        for (int v = 0; v < n; v++) {
            if (indegrees[v] == 1) {
                queue.offer(v);
            }
        }

        // 리프 노드 제거하며 중심 찾기
        while (!queue.isEmpty()) {
            answer.clear();
            int size = queue.size();

            for (int i = 0; i < size; i++) {
                int leaf = queue.poll();
                answer.add(leaf);

                for (int next : tree[leaf]) {
                    indegrees[next]--;

                    if (indegrees[next] == 1)
                        queue.offer(next);
                }
            }
        }

        return answer;
    }
}
```