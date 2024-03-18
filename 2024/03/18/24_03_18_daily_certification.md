# 24_03_18_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 16402 제국**

[16402번: 제국](https://www.acmicpc.net/problem/16402)

왕국 개수와 왕국 이름이 주어진다. 왕국 이름 → 왕국 번호, 왕국 번호 → 왕국 이름을 가져올 수 있어야 하므로 String 배열을 만들어서 왕국 번호에 해당하는 왕국 이름을 찾을 수 있도록 해야 하고, 왕국 이름을 Key, 왕국 번호를 Value로 하는 HashMap을 만들어 왕국 이름에 해당하는 왕국 번호를 찾을 수 있도록 해야 한다.

왕국 이름으로 왕국 번호를 찾을 수 있으면 나머지는 기본적인 분리 집합 문제와 거의 비슷하다. 단 두 왕국이 전쟁을 하는 경우는 두 왕국이 서로 다른 왕국 집합에 속할 경우이거나, 한 왕국 집합에서 해당 왕국 집합의 우두머리인 종주국과 그 자식에 해당하는 속국이 싸우는 경우 뿐이다.

**서로 다른 왕국 집합에 속한 두 왕국이 전쟁할 경우**

결국 각 왕국 집합의 우두머리끼리 전쟁을 하는 것과 같다. 결과에 따라 어떤 왕국이 종주국이 될 지 정하고, 패배한 왕국의 부모를 승리한 왕국으로 하면 된다. 일반적인 Union-Find로 서로 다른 두 분리집합을 합치는 것과 같다.

**한 왕국 집합에서 해당 왕국 집합의 우두머리인 종주국과 그 자식에 해당하는 속국이 싸우는 경우**

어떤 왕국이 종주국이고 어떤 왕국이 속국인지 확인하기 위해서는 두 왕국 모두 find로 속한 왕국 집합의 우두머리를 찾으면 된다. 만약 우두머리가 자기 자신과 같다면 해당 왕국이 종주국이 된다.

종주국, 속국을 정하면 결과에 따라 처리하면 된다.

**종주국이 이겼을 경우**

현재 상황과 달라질 게 없으므로 아무 처리를 할 필요 없다.

**속국이 이겼을 경우**

속국의 부모를 자기 자신으로 하여 속국 스스로 종주국이 되게 한다. 그리고 종주국의 부모를 기존의 속국으로 변경하면 된다.

![ex1.jpeg](24_03_18_daily_certification%20545706d3ef4a490fb2a72071668d9211/ex1.jpeg)

위의 그림에서 종주국은 A, 나머지는 속국이다. A와 F가 전쟁을 한다고 가정하자.

**A가 승리했을 경우** 

위의 상황과 달라질 것이 없다. 실제로는 F의 종주국을 찾기 위한 과정에서 find 연산으로 인해 Path Compression이 일어나 다음 그림처럼 변경된다.

![ex1_1.jpeg](24_03_18_daily_certification%20545706d3ef4a490fb2a72071668d9211/ex1_1.jpeg)

하지만 위 그림처럼 변하는 것은 find 결과로 그렇게 변하는 것일 뿐 F가 A의 속국인 것은 변함이 없으므로 아무런 처리를 하지 않아도 된다.

**F가 승리했을 경우**

위의 상황에서 F의 부모를 F로 변경하여 스스로를 종주국으로 삼는다. 다음 그림같이 된다.

![ex1_2.jpeg](24_03_18_daily_certification%20545706d3ef4a490fb2a72071668d9211/ex1_2.jpeg)

이 상태에서 A의 부모를 F로 변경하면 A가 F의 속국이 된다.

![ex1_3.jpeg](24_03_18_daily_certification%20545706d3ef4a490fb2a72071668d9211/ex1_3.jpeg)

이를 코드로 그대로 구현하면 된다.

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private final static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private final static StringBuilder sb = new StringBuilder();
    private static StringTokenizer tokens;
    
    private static int n;    //    왕국 개수
    private static int m;    //    전투 개수
    
    private static String[] kingdoms;    //    kingdoms[i] : i번째 왕국 이름
    private final static Map<String, Integer> kMap = new HashMap<>();    //    Key : 왕국 이름, Value : 왕국 번호
    
    private static int[] parents;    //    parents[i] : i번째 왕국의 종주국

    private final static List<String> suzerains = new ArrayList<>();    //    종주국 이름 저장 
    
    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        m = Integer.parseInt(tokens.nextToken());
        
        kingdoms = new String[n + 1];
        parents = new int[n + 1];
        
        for(int i = 1; i <= n; i++) {
            kingdoms[i] = br.readLine();
            parents[i] = i;
            kMap.put(kingdoms[i], i);
        }
        
        for(int i = 0; i < m; i++) {
            tokens = new StringTokenizer(br.readLine(), ",");
            String kName1 = tokens.nextToken();    //    첫 번째 왕국 이름
            String kName2 = tokens.nextToken();    //    두 번째 왕국 이름
            int res = Integer.parseInt(tokens.nextToken());
        
            int k1 = kMap.get(kName1);    //    첫 번째 왕국 번호
            int k2 = kMap.get(kName2);    //    두 번쨰 왕국 번호
            
            fight(k1, k2, res);
        }
        
        for(int i = 1; i <= n; i++) {
            if(parents[i] == i)    //    종주국일 경우
                suzerains.add(kingdoms[i]);
        }
        
        Collections.sort(suzerains);
        sb.append(suzerains.size()).append("\n");
        for(String name : suzerains)
            sb.append(name).append("\n");
        
        System.out.print(sb);
    }   //  main-end
    
    private static int findDS(int v) {
        if(v == parents[v])    //    v가 이미 종주국일 경우
            return v;

        return parents[v] = findDS(parents[v]);
    }
    
    private static void fight(int k1, int k2, int res) {
        int ds1 = findDS(k1);    //    첫 번째 왕국의 종주국 번호
        int ds2 = findDS(k2);    //    두 번쨰 왕국의 종주국 번호

        if(ds1 == ds2) {    //    종주국과 속국이 싸울 경우
            if(k1 == ds1) {    //    첫 번째 왕국이 종주국일 경우
                if(res == 1);    //    종주국이 이겼을 경우, 아무 일도 일어나지 않음
                else {    //    속국이 이겼을 경우
                    parents[k2] = k2;    //    속국이었던 k2가 종주국이 됨.
                    parents[k1] = k2;    //    종주국이었던 k1은 k2의 속국이 됨.
                }
            }
            else {    //    두 번째 왕국이 종주국일 경우
                if(res == 1) {    //    속국이 이겼을 경우
                    parents[k1] = k1;    //    속국이었던 k1이 종주국이 됨.
                    parents[k2] = k1;    //    종주국이었던 k2는 k1의 속국이 됨.
                }
            }
        }
        else {    //    서로 다른 국가 집합의 두 국가가 싸울 경우, 두 국가 집합의 종주국끼리 싸움
            if(res == 1)    //    첫 번째 국가 집합이 이겼을 경우    
                parents[ds2] = ds1;
            else
                parents[ds1] = ds2;
        }
    }
}   //  Main-class-end
```