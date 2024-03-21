# 24_03_22_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 17298 오큰수, Programmers 뒤에 있는 큰 수 찾기**

[17298번: 오큰수](https://www.acmicpc.net/problem/17298)

[](https://school.programmers.co.kr/learn/courses/30/lessons/154539)

가장 뒤에 있는 수부터 시작해서 스택에 들어있는 더 뒤에 있는 수들을 확인한다. 만약 스택에 들어있는 수가 현재 수보다 작거나 같을 경우 더 큰 수가 나올 때까지 스택에서 현재 수를 제거한다. 그렇게 해서 나온 수가 현재 수의 오큰수이다. 만약 스택이 빌 때까지 제거했는데도 더 큰 수가 없으면 현재 수의 오큰수는 없다.

현재 수의 오큰수를 찾거나 없음이 판명되면 현재 수를 스택에 넣는다. 현재 수보다 뒤에 있는 수 중 현재 수보다 작은 수들은 스택에서 현재 수 아래에 있으므로 현재 수에 의해 가려지게 되므로 현재 수보다 앞에 있는 어떤 수의 오큰수도 될 수 없다.

예제 2를 통해 알아보자.

![bge21.jpeg](24_03_22_daily_certification%20c9dfe2dde76a45279d1f30e38d0268e3/bge21.jpeg)

처음 가장 뒤에 있는 2보다 뒤에 있는 수를 스택에서 확인한다. 당연히 뒤에는 아무 수도 없으므로 스택도 비어있고 오큰수는 -1이다. 2의 오큰수가 없다는 것을 찾았으니 스택에 넣어준다. 

뒤에서 두 번째 수인 6보다 뒤에 있는 수를 스택에서 확인한다. 2는 6보다 작으므로 스택에서 제거한다. 스택이 비었으므로 6의 오큰수도 없다. 6을 스택에 넣어준다.

3의 오큰수를 찾기 위해 스택에서 확인한다. 6이 3보다 크므로 6이 3의 오큰수이다. 3을 스택에 넣어주자.

![bge22.jpeg](24_03_22_daily_certification%20c9dfe2dde76a45279d1f30e38d0268e3/bge22.jpeg)

3을 스택에 넣은 상태에서 5의 오큰수를 찾기 위해 스택을 확인해보자.

![bge23.jpeg](24_03_22_daily_certification%20c9dfe2dde76a45279d1f30e38d0268e3/bge23.jpeg)

스택에 있는 3은 5보다 작으므로 제거하고 그 다음 스택에 있는 6이 5보다 크므로 5의 오큰수가 된다. 5를 스택에 넣어주자.

![bge24.jpeg](24_03_22_daily_certification%20c9dfe2dde76a45279d1f30e38d0268e3/bge24.jpeg)

1의 오큰수를 찾아보자. 5는 1보다 크고 스택에서 더 최근에 들어온 5가 더 이전에 들어온 6을 가리므로 더 가까운 오른쪽에 있는 5가 1의 오큰수가 된다. 1도 스택에 넣어주자.

![bge25.jpeg](24_03_22_daily_certification%20c9dfe2dde76a45279d1f30e38d0268e3/bge25.jpeg)

마지막으로 9의 오큰수를 찾아보자. 스택에 있는 어떤 수도 9보다 작다. 결국 스택에 있던 모든 수가 스택에서 제거되고 9의 오큰수는 없다. 9가 스택에 들어가긴 하지만 어차피 모든 수의 오큰수를 찾았으므로 별 의미는 없다.

**뒤에 있는 큰 수 찾기 정답 코드**

```java
import java.util.Stack;
import java.util.Arrays;

class Solution {
    public int[] solution(int[] numbers) {
        int len = numbers.length;   //  정수 배열 길이
        int[] answer = new int[len];
        Arrays.fill(answer, -1);
        
        Stack<Integer> stack = new Stack<>();
        
        //  가장 마지막 수 제외 오른쪽 끝의 수부터 확인
        for(int idx = len - 1; idx >= 0; idx--) {
            int num = numbers[idx]; //  현재 수
            
            while(!stack.isEmpty()) {                       
                int top = stack.peek(); //  스택에 가장 위에 있는 수
                
                if(num >= top)   //  현재 수가 스택 가장 위에 있는 수보다 작지 않을 경우
                    stack.pop();    //  스택에서 가장 위에 있는 수 제거
                else {  //  현재 수보다 스택 가장 위에 있는 수가 더 클 경우 그 수가 현재 수의 뒷큰수임
                    answer[idx] = top;  //  스택 가장 위에 있는 수를 뒷큰수로 삼기
                    break;  //  뒷큰수 찾았으므로 스택 더 볼 필요 없음
                }
            }
            
            stack.push(num);    //  현재 수를 스택에 넣음
        }
        
        
        return answer;
    }
}
```

**Programmers 합승 택시 요금**

[](https://school.programmers.co.kr/learn/courses/30/lessons/72413#)

정점 개수 N이 최대 200이고, 간선 개수 E도 최대 N * (N - 1) / 2이므로 O(ElogN) = O(N^2logN)의 Dijkstra Algorithm도 괜찮고, O(N^3)의 Floyd Warshall Algorithm도 괜찮다.

Dijkstra Algorithm을 사용할 경우 합승해서 갈 정점을 mid라고 할 때, 1~n까지 모든 가능한 mid에 대해 s에서 mid까지, mid에서 a, b까지 3번 Dijkstra Algorithm을 수행할 경우 시간 복잡도는 O(NElogN) = O(N^3logN)이 된다. N이 워낙 작아서 큰 문제가 없어보이지만 비효율적이다.

그래프가 양방향 그래프이고 임의의 두 정점 a, b가 연결되어 있을 경우 a에서 b로 가는 비용과 b에서 a로 가는 비용은 같다.

![dijkstra.jpeg](24_03_22_daily_certification%20c9dfe2dde76a45279d1f30e38d0268e3/dijkstra.jpeg)

즉 임의의 mid에서 s, a, b로 가는 비용은 각각 s, a, b에서 mid로 가는 비용과 같으므로 s, a, b를 출발 정점으로 놓고 3번의 Dijkstra Algorithm을 수행하면 더 이상 수행할 필요가 없이 모든 경우의 비용을 구할 수 있다. mid만 1~n으로 바꿔가며 이미 구한 비용들을 이용해 계산하면 된다.

s-mid, a-mid, b-mid 사이 간선이 없을 경우를 잘 대비해야 한다.

**Dijkstra 코드**

```java
import java.util.PriorityQueue;
import java.util.List;
import java.util.Arrays;
import java.util.ArrayList;

class Solution {
    private final static int INF = Integer.MAX_VALUE;
    
    private List<Node>[] graph;
    
    public int solution(int n, int s, int a, int b, int[][] fares) {
        int answer = 0;
        
        graph = new ArrayList[n + 1];
        for(int i = 1; i <= n; i++)
            graph[i] = new ArrayList<>();
        
        for(int i = 0; i < fares.length; i++) { //  모든 간선 확인
            int v1 = fares[i][0];
            int v2 = fares[i][1];
            int cost = fares[i][2]; //  v1, v2 사이 간선 비용 cost
            
            graph[v1].add(new Node(v2, cost));
            graph[v2].add(new Node(v1, cost));  //   양방향 그래프
        }
        
        int[] distS = dijkstra(s, n);   //  s로부터 각 정점 사이 비용
        int[] distA = dijkstra(a, n);   //  a로부터 각 정점 사이 비용
        int[] distB = dijkstra(b, n);   //  b로부터 각 정점 사이 비용
        
        answer = INF;   //  최소 비용
        
        for(int mid = 1; mid <= n; mid++) { //  같이 타고 갈 곳 mid
            int sToMid = distS[mid];    //  s부터 mid까지 비용
            int midToA = distA[mid];    //  mid부터 a까지 비용 = a부터 mid까지 비용 (양방향 그래프)
            int midToB = distB[mid];    //  mid부터 b까지 비용 = b부터 mid까지 비용 (양방향 그래프)
            
            if(sToMid == INF || midToA == INF || midToB == INF) //  mid를 거쳐서 갈 수 없을 겨우
                continue;
            
            answer = Math.min(answer, sToMid + midToA + midToB);
        }
        
        return answer;
    }
    
    //  start로부터 n개의 정점까지 가는 최소 비용 리스트 반환
    private int[] dijkstra(int start, int n) {
        PriorityQueue<Node> pq = new PriorityQueue<>();
        
        int[] dist = new int[n + 1];
        Arrays.fill(dist, INF);
        
        dist[start] = 0;    //  start에서 start까지 비용은 0
        pq.offer(new Node(start, dist[start]));
        
        while(!pq.isEmpty()) {
            Node curNode = pq.poll();
            int cv = curNode.vertex;    //  현재 정점
            int ccost = curNode.cost;   //  현재 정점까지의 비용
            
            if(dist[cv] < ccost)    //  현재 cv까지 알려진 최소비용보다 더 클 경우 고려할 가치가 없음
                continue;
            
            for(Node nextNode : graph[cv]) {    //  cv와 연결된 다른 정점 확인
                int nv = nextNode.vertex;               //  다음 정점
                int ncost = ccost + nextNode.cost;      //  cv를 거쳐서 nv까지 가는 비용
                
                if(ncost < dist[nv]) {  //  기존에 알려진 nv까지 최소 비용보다 적을 경우
                    dist[nv] = ncost;   //  최소 비용 갱신
                    pq.offer(new Node(nv, dist[nv]));
                }
            }
        }
        
        return dist;
    }
    
    private static class Node implements Comparable<Node> {
        int vertex;
        int cost;
        
        Node(int vertex, int cost) {
            this.vertex = vertex;
            this.cost = cost;
        }
        
        //  cost에 대해 오름차순 정렬
        @Override
        public int compareTo(Node node) {
            return Integer.compare(this.cost, node.cost);
        }
    }
}
```

정점의 개수 n이 최대 200이므로 간선 개수는 많아야 19,900개이다. 모든 간선의 비용이 최대 비용 100,000이라고 쳐도 가능한 임의의 두 정점 사이의 최대 비용은 1,990,000,000이므로 20억이 되지 않아 int형으로 충분하다.

혹시 이런 계산이 자신이 없거나 가늠하기 어려운 경우에는 long을 사용하자. 어지간한 경우에는 별 문제 없을 것이다. 아래 Floyd-Warshall 코드는 long으로 작성했다.

**Floyd-Warshall 코드**

```java
import java.util.Arrays;

class Solution {
    private final static long INF = Long.MAX_VALUE;
    
    private long[][] dist;   //  dist[v1][v2]    //  v1에서 v2로 가는 최소 비용
    
    public int solution(int n, int s, int a, int b, int[][] fares) {
        long answer = INF;
        
        dist = new long[n + 1][n + 1];
        for(int i = 1; i <= n; i++) {
            Arrays.fill(dist[i], INF);
            dist[i][i] = 0; //  자기 자신으로 가는 비용은 0
        }
        
        for(int i = 0; i < fares.length; i++) {
            int v1 = fares[i][0];
            int v2 = fares[i][1];
            int cost = fares[i][2]; //  두 정점 v1, v2 사이 간선 비용 cost
            
            dist[v1][v2] = dist[v2][v1] = cost;
        }
        
        floydWarshall(n);
        
        for(int mid = 1; mid <= n; mid++) { //  같이 가는 지점 mid
            long sToMid = dist[s][mid];
            long midToA = dist[mid][a];
            long midToB = dist[mid][b];
            
            if(sToMid == INF || midToA == INF || midToB == INF) //  mid를 거칠 수 없는 경우
                continue;
            
            answer = Math.min(answer, sToMid + midToA + midToB);
        }
        
        return (int)answer;
    }
    
    private void floydWarshall(int n) {
        for(int k = 1; k <= n; k++) {
            for(int i = 1; i <= n; i++) {
                for(int j = 1; j <= n; j++) {
                    if(dist[i][k] == INF || dist[k][j] == INF)  //  k를 거쳐서 갈 수 없을 경우
                        continue;
                    
                    dist[i][j] = Math.min(dist[i][j], dist[i][k] + dist[k][j]);
                }
            }
        }
    }
}
```