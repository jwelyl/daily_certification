# 25_04_11_daily_certification

# Problem Solving (Algorithm & SQL)

### [PCCP 모의고사 #1] 4번 - 운영체제

[](https://school.programmers.co.kr/learn/courses/15008/lessons/121686)

**나의 코드 (틀렸습니다 + 시간초과)**

```java
import java.util.PriorityQueue;
import java.util.Arrays;

class Solution {
    private final long[] answer = new long[11];
    
    //  호출된 프로세스들이 대기하는 큐
    private final PriorityQueue<int[]> pq = new PriorityQueue<>(
        (p1, p2) ->
            //  점수가 작은 프로세스, 점수가 같을 경우 호출 시간이 빠른 프로세스 먼저 실행
            p1[0] == p2[0] ? Integer.compare(p1[1], p2[1]) : Integer.compare(p1[0], p2[0])
    );
    
    private int N;                  //  프로세스 수
    private long totalTime = 0;      //  프로세스 모두 실행되는데 걸리는 시간
    private int idx = 1;
    
    private int terminated = 1;     //  실행 후 종료된 프로세스 수
    
    private int[][] processes;
    
    public long[] solution(int[][] program) {
        processes = program;
        N = processes.length;
        
        //  호출이 빠른 순서대로, 호출 시간이 같을 경우 점수가 낮은 순서대로 정랼
        Arrays.sort(processes, (p1, p2) -> p1[1] == p2[1] ? Integer.compare(p1[0], p2[0]) : Integer.compare(p1[1], p2[1]));

        // for(int[] process : processes) {
        //     System.out.println("process : [" + process[0] + ", " + process[1] + ", " + process[2] + "]");
        // }
        
        totalTime += processes[0][2];
        
        while(terminated < N) {
            while(idx < N) {
                if(processes[idx][1] <= totalTime) { //  현재 시간이 될 동안 프로세스들이 호출됐을 경우
                    pq.offer(processes[idx]);        //  호출되서 대기 큐에 저장된 상태
                    idx++;
                }
                else break; //  아직 호출되지 않았음, 이후 프로세스들도 마찬가지
            }
            
            if(!pq.isEmpty()) {
                int[] process = pq.poll();  //  대기 큐 저장된 프로세스 중 가장 빨리 실행될 프로세스
                
                // System.out.println("이번에 실행될 프로세스 : [" + process[0] + ", " + process[1] + ", " + process[2] + "]");
                
                int score = process[0];     //  해당 프로세스 점수
                long waitTime = totalTime - process[1];  //  해당 프로세스가 대기한 시간
                int runtime = process[2];   //  해당 프로세스 실행 시간

                answer[score] += waitTime;
                totalTime += runtime;
                terminated++;
            }
        }
        
        answer[0] = totalTime;
        return answer;
    }
}
```

**정답 코드**

```java
import java.util.PriorityQueue;

class Solution {
    private long[] answer = new long[11];
    
    //  주어진 프로세스들
    private final PriorityQueue<Process> processes = new PriorityQueue<>(
        (p1, p2) -> Integer.compare(p1.call, p2.call)   //  호출 순서가 빠른 순으로 대기 큐에 들어감
    );
    //  호출된 후 대기 중인 프로세스들
    private final PriorityQueue<Process> waitings = new PriorityQueue<>(
        (p1, p2) -> {
            int res = Integer.compare(p1.score, p2.score);  //  점수가 낮은 프로세스부터 실행됨
            
            if(res == 0)
                res = Integer.compare(p1.call, p2.call);    //  점수가 같을 경우 먼저 호출된 프로세스부터 실행됨
        
            return res;
        }
    );
    
    private long totalTime = 0;
    
    public long[] solution(int[][] program) {
        for(int[] p : program)
            processes.offer(new Process(p[0], p[1], p[2]));
        
        while(!processes.isEmpty() || !waitings.isEmpty()) {   //  아직 대기큐에 들어가지 못한 프로세스가 있을 경우
            while(!processes.isEmpty() && processes.peek().call <= totalTime)
               waitings.offer(processes.poll());
            
            if(waitings.isEmpty()) { //  totalTime = 0이라서 어떤 프로세스도 대기큐에 들어오지 못한 경우
                totalTime = processes.peek().call;
                continue;
            }
            
            Process p = waitings.poll();
            answer[p.score] += (totalTime - p.call);
            totalTime += p.runtime;
        }
        
        answer[0] = totalTime;
        
        return answer;
    }
    
    private static class Process {
        public final int score;     //  프로세스 점수
        public final int call;      //  프로세스 호출 시각
        public final int runtime;   //  프로세스 실행 시간
        
        public Process(int score, int call, int runtime) {
            this.score = score;
            this.call = call;
            this.runtime = runtime;
        }
    }
}
```