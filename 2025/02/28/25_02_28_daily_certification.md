# 25_02_28_daily_certification

# Problem Solving (Algorithm & SQL)

### 프로그래머스 광물 캐기

[](https://school.programmers.co.kr/learn/courses/30/lessons/172927)

```java
import java.util.Arrays;

class Solution {
    public int solution(int[] picks, String[] minerals) {
        int totalCnt = (picks[0] + picks[1] + picks[2]) * 5; //  캘 수 있는 최대 광물 수 = 곡괭이 총 개수 * 5
        int answer = 0;
        
        totalCnt = Math.min(totalCnt, minerals.length); //  실제 캘 수 있는 광물 수 구하기
        
        int q = totalCnt / 5;    //  광물들을 순서대로 5개씩 묶었을때 묶음 수
        int r = totalCnt % 5;    //  5개씩 묶고 남은 광물 수 -> 한 묶음
        
        //  fatigues[i] : i번째 광물 묶음을 채굴할 때 {다이아몬드 곡괭이로 채굴했을떄 피로도, 철 곡괭이로 채굴했을때 피로도, 돌 곡괭이로 채굴했을때 피로도}
        int[][] fatigues = new int[q + (r != 0 ? 1 : 0)][3];
        
        int idx = 0;
        
        for(idx = 0; idx < q; idx++) {
            for(int i = 5 * idx; i < 5 * idx + 5; i++) {
                if(minerals[i].equals("diamond")) {
                    fatigues[idx][0] += 1;
                    fatigues[idx][1] += 5;
                    fatigues[idx][2] += 25;
                }
                else if(minerals[i].equals("iron")) {
                    fatigues[idx][0] += 1;
                    fatigues[idx][1] += 1;
                    fatigues[idx][2] += 5;
                }
                else {
                    fatigues[idx][0] += 1;
                    fatigues[idx][1] += 1;
                    fatigues[idx][2] += 1;
                }
            }
        }
        
        
        for(int i = 5 * idx; i < 5 * idx + r; i++) {
            if(minerals[i].equals("diamond")) {
                fatigues[idx][0] += 1;
                fatigues[idx][1] += 5;
                fatigues[idx][2] += 25;
            }
            else if(minerals[i].equals("iron")) {
                fatigues[idx][0] += 1;
                fatigues[idx][1] += 1;
                fatigues[idx][2] += 5;
            }
            else {
                fatigues[idx][0] += 1;
                fatigues[idx][1] += 1;
                fatigues[idx][2] += 1;
            }
        }
        
        //  5개씩 묶었을때 피로도가 큰 조합 순으로 정렬
        Arrays.sort(fatigues, (f1, f2) -> Integer.compare(f2[2], f1[2]));
        
        for(int i = 0; i < fatigues.length; i++) {
            if(picks[0] != 0) { //  다이아몬드 곡괭이가 있을경우
                answer += fatigues[i][0];
                picks[0]--;
            }
            else if(picks[1] != 0) {    //  다이아몬드 곡괭이는 없고, 철 곡괭이가 있을 경우
                answer += fatigues[i][1];
                picks[1]--;
            }
            else if(picks[2] != 0) {    //  돌 곡괭이만 있을 경우
                answer += fatigues[i][2];
                picks[2]--;
            }
            else break; //  더 이상 곡괭이가 없을 경우
        }
        
        return answer;
    }
}
```

### 프로그래머스 전력망을 둘로 나누기

[](https://school.programmers.co.kr/learn/courses/30/lessons/86971)

```java
import java.util.List;
import java.util.ArrayList;

class Solution {
    private final int ROOT = 1;
    
    private List<Integer>[] tree;
    private int[] depthArr;         //  depthArr[v] : ROOT의 깊이를 1이라 했을때 v 노드의 깊이
    private int[] sizeArr;          //  sizeArr[v] : v 노드를 루트 노드로 하는 서브트리에 포함된 노드 수
    
    public int solution(int n, int[][] wires) {
        int answer = n;
        
        tree = new ArrayList[n + 1];
        depthArr = new int[n + 1];
        sizeArr = new int[n + 1];
        
        for(int v = 1; v <= n; v++)
            tree[v] = new ArrayList<>();
        
        for(int[] wire : wires) {
            int v1 = wire[0];
            int v2 = wire[1];
            
            tree[v1].add(v2);
            tree[v2].add(v1);
        }
        
        dfs(ROOT, 1);
        
        for(int[] wire : wires) {   //  wire를 절단한다고 가정
            int v1 = wire[0];
            int v2 = wire[1];
            
            int child = depthArr[v1] > depthArr[v2] ? v1 : v2;
            int childSize = sizeArr[child];                     //  child를 루트로 하는 서브트리 크기
            int remainSize = (sizeArr[ROOT] - childSize);       //  child 서브트리가 분리되고 남은 크기
            
            answer = Math.min(answer, Math.abs(childSize - remainSize));
        }
        
        return answer;
    }
    
    private void dfs(int vertex, int depth) {
        sizeArr[vertex] = 1;
        depthArr[vertex] = depth;
        
        for(int next : tree[vertex]) {
            if(depthArr[next] == 0) {   //  next를 아직 방문하지 않았을 경우
                dfs(next, depth + 1);   //  next는 vertex의 자식임
                sizeArr[vertex] += sizeArr[next];
            }
        }
    }
}
```