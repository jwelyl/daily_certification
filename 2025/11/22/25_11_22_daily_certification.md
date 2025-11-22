# 25_11_22_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 14452 **Cow Dance Show**

[14452번: Cow Dance Show](http://boj.ma/14452/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.PriorityQueue;

public class Main {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());
        int n = Integer.parseInt(st.nextToken());
        int maxT = Integer.parseInt(st.nextToken());
        
        //    dArr[i] : i번째로 입장하는 소가 춤추는 시간
        int[] dArr = new int[n];
        for(int i = 0; i < n; i++)
            dArr[i] = Integer.parseInt(br.readLine());
        
        System.out.println(parametricSearch(n, maxT, dArr));
    }    //    main-end
    
    private static int parametricSearch(int n, int maxT, int[] dArr) {
        int start = 1;
        int end = n;
        int res = n;
        
        while(start <= end) {
            int mid = start + (end - start) / 2;
            
            if(ok(mid, n, maxT, dArr)) {    //    최초에 mid 마리 입장해도 가능할 경우
                res = mid;                  //    일단 정답 후보
                end = mid - 1;              //    최초에 mid 마리보다 더 적게 입장해도 되는지 확인
            }
            else                            //    최초에 mid 마리 입장하면 불가능할 경우
                start = mid + 1;            //    최초에 mid 마리보다 더 많이 입장해야 함
        }
        
        return res;
    }
    
    private static boolean ok(int cnt, int n, int maxT, int[] dArr) {
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        int order = 0;
        int time = 0;
        
        while(order < cnt) {
            int endTime = time + dArr[order];
            if(endTime > maxT)
                return false;
            
            pq.offer(endTime);
            order++;
        }
        
        while(!pq.isEmpty()) {
            time = pq.poll();    //    먼저 올라간 cnt마리 중 가장 먼저 끝나는 소의 끝나는 시간
            
            if(order < n) {
                int endTime = time + dArr[order];
                if(endTime > maxT)
                    return false;
                
                pq.offer(endTime);
                order++;
            }
        }
        
        return true;
    }
}    //    Main-class-end
```

### BOJ 5624 좋은 수

[5624번: 좋은 수](http://boj.ma/5624/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashSet;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st;

    int n = Integer.parseInt(br.readLine());
    int[] sequence = new int[n];
    Set<Integer> set = new HashSet<>();
    int answer = 0;

    st = new StringTokenizer(br.readLine());
    for(int i = 0; i < n; i++)
      sequence[i] = Integer.parseInt(st.nextToken());

    for(int d = 0; d < n; d++) {
      boolean ok = false;
      for(int c = 0; c < d; c++) {
        if(set.contains(sequence[d] - sequence[c])) {
          ok = true;
          break;
        }
      }

      if(ok)
        answer++;

      for(int a = 0; a <= d; a++)
        set.add(sequence[a] + sequence[d]);
    }

    System.out.println(answer);
  }    //    main-end
}    //    Main-class-end
```