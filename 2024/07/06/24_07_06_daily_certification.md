# 24_07_06_daily_certification

# Network

# Application Layer (L7)

# DNS

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Application%20Layer%20(L7)%20fa10ec7efe604c1eae621586f911eb67/Untitled.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Application%20Layer%20(L7)%20fa10ec7efe604c1eae621586f911eb67/Untitled%201.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Application%20Layer%20(L7)%20fa10ec7efe604c1eae621586f911eb67/Untitled%202.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Application%20Layer%20(L7)%20fa10ec7efe604c1eae621586f911eb67/Untitled%203.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Application%20Layer%20(L7)%20fa10ec7efe604c1eae621586f911eb67/Untitled%204.png)

# Web

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Application%20Layer%20(L7)%20fa10ec7efe604c1eae621586f911eb67/Untitled%205.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Application%20Layer%20(L7)%20fa10ec7efe604c1eae621586f911eb67/Untitled%206.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Application%20Layer%20(L7)%20fa10ec7efe604c1eae621586f911eb67/Untitled%207.png)

# URL, URI

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Application%20Layer%20(L7)%20fa10ec7efe604c1eae621586f911eb67/Untitled%208.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Application%20Layer%20(L7)%20fa10ec7efe604c1eae621586f911eb67/Untitled%209.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Application%20Layer%20(L7)%20fa10ec7efe604c1eae621586f911eb67/Untitled%2010.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Application%20Layer%20(L7)%20fa10ec7efe604c1eae621586f911eb67/Untitled%2011.png)

# HTTP

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Application%20Layer%20(L7)%20fa10ec7efe604c1eae621586f911eb67/Untitled%2012.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Application%20Layer%20(L7)%20fa10ec7efe604c1eae621586f911eb67/Untitled%2013.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Application%20Layer%20(L7)%20fa10ec7efe604c1eae621586f911eb67/Untitled%2014.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Application%20Layer%20(L7)%20fa10ec7efe604c1eae621586f911eb67/Untitled%2015.png)

# Web Service

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Application%20Layer%20(L7)%20fa10ec7efe604c1eae621586f911eb67/Untitled%2016.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Application%20Layer%20(L7)%20fa10ec7efe604c1eae621586f911eb67/Untitled%2017.png)

# Problem Solving (Algorithm & SQL)

### BOJ 21773 가희와 프로세스 1

[](https://www.acmicpc.net/problem/21773)

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer tokens;
    
    private static final PriorityQueue<Process> scheduler = new PriorityQueue<>();
    
    private static int T;
    private static int N;
    
    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        T = Integer.parseInt(tokens.nextToken());
        N = Integer.parseInt(tokens.nextToken());
        
        for(int i = 0; i < N; i++) {
            tokens = new StringTokenizer(br.readLine());
            scheduler.offer(new Process(Integer.parseInt(tokens.nextToken()), Integer.parseInt(tokens.nextToken()), Integer.parseInt(tokens.nextToken())));
        }
        
        for(int t = 0; t < T; t++) {
            Process now = scheduler.poll();    //    현재 실행될 프로세스
            now.priority--;    //    현재 실행된 프로세스 우선순위 1 감소 (다른 프로세스 우선순위 1 증가 효과)
            now.time--;        //    현재 프로세스가 종료될 때까지 필요한 시간 1 감소
            
            sb.append(now.id).append("\n");
            
            if(now.time > 0)   //    아직 더 실행되어야 하는 경우
                scheduler.offer(now);
        }
        
        System.out.print(sb);
    }   //  main-end
    
    private static class Process implements Comparable<Process> {
        public int id;
        public int priority;
        public int time;
        
        public Process(int id, int time, int priority) {
            this.id = id;
            this.time = time;
            this.priority = priority;
        }
        
        @Override
        public int compareTo(Process other) {
            int ret = Integer.compare(other.priority, this.priority);
            
            if(ret == 0)
                ret = Integer.compare(this.id, other.id);
        
            return ret;
        }
    }
}   //  Main-class-end
```