# 24_03_21_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 13140 Hello World!**

[13140번: Hello World!](https://www.acmicpc.net/problem/13140)

h, w, e, o, l, r, d 총 7개의 문자에 각각 0~9 사이의 수를 하나씩 배정한다. 서로 같은 수가 배정된 두 문자가 존재해서는 안되며 h, w는 가장 앞의 자릿수가 될 것이므로 0일 수 없다.

브루트포스로 모든 경우의 수를 따져도 약 10^7의 경우의 수가 된다. 굳이 backtracking을 사용하지 않고 7중 for문으로도 해결할 수 있다.

**틀린 코드**

```java
import java.io.*;

public class Main {
    private static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static int n;
    private final static boolean[] used = new boolean[10];
    
    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());
        
        for(int d = 0; d < 10; d++) {
            used[d] = true;
            for(int e = 0; e < 10; e++) {
                if(used[e]) continue;
                used[e] = true;
                
                for(int h = 1; h < 10; h++) {
                    if(used[h]) continue;
                    used[h] = true;
                    
                    for(int l = 0; l < 10; l++) {
                        if(used[l]) continue;
                        used[l] = true;
                        
                        for(int o = 0; o < 10; o++) {
                            if(used[o]) continue;
                            used[o] = true;
                            
                            for(int r = 0; r < 10; r++) {
                                if(used[r]) continue;
                                
                                for(int w = 1; w < 10; w++) {
                                    if(used[w]) continue;
                                    
                                    if((h * 10_000 + e * 1_000 + l * 100 + l * 10 + o) + (w * 10_000 + o * 1_000 + r * 100 + l * 10 + d) == n) {
                                        print(d, e, h, l, o, r, w);
                                        return;
                                    }
                                    
                                    used[w] = false;
                                }   //  w-end
                                
                                used[r] = false;
                            }   //  r-end
                            
                            used[o] = false;
                        }   //  o-end
                        
                        used[l] = false;
                    }   //  l-end
                    
                    used[h] = false;
                }   //  h-end
                
                used[e] = false;
            }   //  e-end 
            
            used[d] = false;
        }   //  d-end
        
        System.out.println("No Answer");
    }    //   main-end
    
    private static void print(int d, int e, int h, int l, int o, int r, int w) {
        System.out.println("  " + h + e + l + l + o);
        System.out.println("+ " + w + o + r + l + d);
        System.out.println("-------");
        System.out.printf("%7d%n", n);
    }
}   //   Main-class-end
```

하지만 위의 코드는 틀렸다. used[w] = true 처리를 누락했기 때문이다. 바로 틀리는 것도 아니고 85%쯤 가서 틀려서 의심하기 어려웠고, 실제 시험이면 예제만 맞았다고 제출할 것이기 때문에 더욱 발견할 가능성이 낮다.

이렇게 단순하고 반복적인 코드 작성은 실수할 가능성이 높고, 그렇게 발생한 실수를 못 찾을 가능성은 더더욱 높다.

아무튼 위 코드를 맞게 고치면

**맞은 코드**

```java
import java.io.*;

public class Main {
    private static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static int n;
    private final static boolean[] used = new boolean[10];
    
    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());
        
        for(int d = 0; d < 10; d++) {
            used[d] = true;
            for(int e = 0; e < 10; e++) {
                if(used[e]) continue;
                used[e] = true;
                
                for(int h = 1; h < 10; h++) {
                    if(used[h]) continue;
                    used[h] = true;
                    
                    for(int l = 0; l < 10; l++) {
                        if(used[l]) continue;
                        used[l] = true;
                        
                        for(int o = 0; o < 10; o++) {
                            if(used[o]) continue;
                            used[o] = true;
                            
                            for(int r = 0; r < 10; r++) {
                                if(used[r]) continue;
                                used[r] = true;
                                
                                for(int w = 1; w < 10; w++) {
                                    if(used[w]) continue;
                                    
                                    if((h * 10_000 + e * 1_000 + l * 100 + l * 10 + o) + (w * 10_000 + o * 1_000 + r * 100 + l * 10 + d) == n) {
                                        print(d, e, h, l, o, r, w);
                                        return;
                                    }
                                }   //  w-end
                                
                                used[r] = false;
                            }   //  r-end
                            
                            used[o] = false;
                        }   //  o-end
                        
                        used[l] = false;
                    }   //  l-end
                    
                    used[h] = false;
                }   //  h-end
                
                used[e] = false;
            }   //  e-end 
            
            used[d] = false;
        }   //  d-end
        
        System.out.println("No Answer");
    }    //   main-end
    
    private static void print(int d, int e, int h, int l, int o, int r, int w) {
        System.out.println("  " + h + e + l + l + o);
        System.out.println("+ " + w + o + r + l + d);
        System.out.println("-------");
        System.out.printf("%7d%n", n);
    }
}   //   Main-class-end
```

h, w, e, o, l, r, d  알파벳에 수 배정 순서는 관계가 없다. 테스트해봤다.

아무튼 위와 같은 반복적인 코드는 실수를 유발할 가능성이 매우 높으므로 우아하게 풀어보자.

**백트래킹 이용 코드**

```java
import java.io.*;

public class Main {
  private static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

  private static int n;
  private final static int[] nums = {-1, -1, -1, -1, -1, -1, -1}; //  각각 h, w, e, o, l, r, d에 배정될 수, 미배정 시 -1
  private final static boolean[] visited = new boolean[10];

  private static boolean find = false;    //    찾은 경우 true

  public static void main(String[] args) throws IOException {
    n = Integer.parseInt(br.readLine());

    dfs(0);

    if(!find)
      System.out.println("No Answer");
  }   //  main-end

  private static void dfs(int depth) {
    if(find)    //    이미 되는 경우를 찾은 경우
      return;

    if(nums[0] == 0 || nums[1] == 0)    //    탐색 시작하고 h 또는 w가 0일 경우 가지치기
      return;

    if(depth == 7) {    //    완성됐을 경우
      int hello = nums[0] * 10_000 + nums[2] * 1000 + nums[4] * 110 +nums[3];
      int world = nums[1] * 10_000 + nums[3] * 1000 + nums[5] * 100 + nums[4] * 10 + nums[6];

      if(hello + world == n) {
        find = true;

        System.out.printf("%7d\n", hello);
        System.out.printf("+%6d\n", world);
        System.out.println("-------");
        System.out.printf("%7d\n", n);
      }

      return;
    }

    for(int digit = 0; digit < 10; digit++) {
      if(!visited[digit]) {    //   아직 digit이 쓰이지 않았을 경우
        nums[depth] = digit;
        visited[digit] = true;
        dfs(depth + 1);
        visited[digit] = false;
      }
    }
  }
}   //  Main-class-end
```

탐색이 시작되기 전 각 알파벳을 -1로 초기화하고, 탐색이 시작된 후 h 또는 d에 0이 배정되면 탐색을 바로 중단하도록 구현했다. 만약 -1로 초기화하지 않으면 시작하자마자 h = 0, d = 0이어서 탐색이 중단되어 버린다. 반복되는 로직을 dfs로 구현하니 코드 양도 적어져서 실수를 빠르게 찾을 수 있다.