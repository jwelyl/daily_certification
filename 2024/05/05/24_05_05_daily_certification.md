# 24_05_05_daily_certification

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 초대장과 번호표**

[https://www.codetree.ai/missions/8/problems/c-tag/description](https://www.codetree.ai/missions/8/problems/c-tag/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/c-tag/description)

**코드**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.*;

public class Main {
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static StringTokenizer tokens;

  private static int n;   //  n명의 사람
  private static int g;   //  그룹 개수
  private static boolean[] invited;     //  invited[i] : i번째 사람이 초대받았으면 true
  private static Set<Integer>[] groups; //  groups[i] : i번째 그룹에 속한 사람들 집합
  private static List<Integer>[] peopleGroups;  //  peopleGroups[i] : i번째 사람이 속한 그룹

  private static int ans = 0; //  가장 큰 목장 한 변 길이

  public static void main(String[] args) throws IOException {
    tokens = new StringTokenizer(br.readLine());

    n = Integer.parseInt(tokens.nextToken());
    g = Integer.parseInt(tokens.nextToken());

    invited = new boolean[n + 1];
    groups = new HashSet[g + 1];
    for(int i = 0; i <= g; i++)
      groups[i] = new HashSet<>();

    peopleGroups = new ArrayList[n + 1];
    for(int i = 0; i <= n; i++)
      peopleGroups[i] = new ArrayList<>();

    for(int i = 1; i <= g; i++) {
      tokens = new StringTokenizer(br.readLine());
      int cnt = Integer.parseInt(tokens.nextToken()); //  i 그룹에 속한 사람 수

      for(int j = 0; j < cnt; j++) {
        int person = Integer.parseInt(tokens.nextToken());  //  i 그룹에 속한 사람
        groups[i].add(person);          //  i번째 그룹에 person이 속함
        peopleGroups[person].add(i);    //  person이 속한 그룹에 i 추가
      }
    }

    bfs();

    System.out.println(ans);
  } // main-end

  private static void bfs() {
    Queue<Integer> q = new LinkedList<>();
    invited[1] = true;  //  1번 사람은 초대됨
    q.offer(1);

    while(!q.isEmpty()) {
      int cur = q.poll(); //  초대된 사람
      ans++;

      for(int i = 0; i < peopleGroups[cur].size(); i++) {
        int group = peopleGroups[cur].get(i); //  cur이 포함된 그룹 번호
        groups[group].remove(cur);  //  cur이 포함된 그룹에서 cur 지우기

        if(groups[group].size() == 1) { //  해당 그룹에 초대받지 않은 사람이 한 명만 남았다면
          int next = new ArrayList<>(groups[group]).get(0); //  해당 사람을 초대함

          if(!invited[next]) {  //  아직 초대받지 않은 사람이면
            invited[next] = true;
            q.offer(next);
          }
        }
      }
    }
  } //  bfs-end
} // Main-class-end
```