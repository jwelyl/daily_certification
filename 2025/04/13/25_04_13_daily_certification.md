# 25_04_13_daily_certification

# Problem Solving (Algorithm & SQL)

### [PCCP 모의고사 #1] 4번 - 운영체제

[](https://school.programmers.co.kr/learn/courses/15008/lessons/121686)

```java
import java.util.PriorityQueue;
import java.util.List;
import java.util.ArrayList;
import java.util.Collections;

class Solution {
    private final long[] answer = new long[11];
    
    //  전체 프로세스
    private final List<Process> processes = new ArrayList<>();
    
    //  호출된 이후 대기 큐
    private final PriorityQueue<Process> waitings = new PriorityQueue<>(
        (p1, p2) -> {
            int res = Integer.compare(p1.score, p2.score);  //  점수가 낮은 프로세스 우선
            if(res == 0)    //  점수가 같을 경우
                res = Integer.compare(p1.callTime, p2.callTime);    //  호출 시간이 빠른 프로세스 우선
            return res;
        }
    );
    
    private int idx = 0;            //  waitings에 들어갈 process 인덱스
    private long curTime = 0;       //  프로세스가 하나씩 실행된 시간
    
    public long[] solution(int[][] program) {
        for(int[] p : program)
            processes.add(new Process(p[0], p[1], p[2]));
        
        //  호출 시간이 빠른 순서대로 waitings에 들어감
        Collections.sort(processes, (p1, p2) -> {
                int res = Integer.compare(p1.callTime, p2.callTime);  //  호출 시간이 빠른 프로세스 우선
                if(res == 0)    //  호출 시간이 같을 경우
                    res = Integer.compare(p1.score, p2.score);    //  점수가 작은 프로세스 우선
                return res;
            }              
        );
        
        //  가장 호출 시간이 빠른 프로세스(여러 개일 경우 그 중 점수가 가장 낮은 프로세스) 실행, 이 프로세스는 들어오자 마자 실행되므로 대기 시간 없음
        Process p = processes.get(idx++);
        curTime = p.callTime + p.runtime;
        
        //  아직 대기큐에 들어가지 않은 프로세스가 남았거나, 대기큐에 프로세스가 남은 경우
        while(idx < processes.size() || !waitings.isEmpty()) {
            while(idx < processes.size()) { //  아직 waitings에 들어가지 않은 전체 프로세스 확인
                p = processes.get(idx);
                
                if(p.callTime <= curTime) {  //  p가 curTime 안에 호출됐을 경우
                    idx++;      
                    waitings.offer(p);       //  p가 waitings에 들어감
                }
                else    //  p가 curTime 안에 호출되지 않는 경우
                    break;
            }
            
            //  waitings에 들어있는 프로세스가 처리될 동안 호출되서 waitings에 들어간 프로세스가 존재하지 않을 경우
            if(waitings.isEmpty()) {
                p = processes.get(idx++);
                curTime = p.callTime;
                waitings.offer(p);   //  waitings에 들어오지 않은 프로세스 중 가장 호출 시간이 빠른 프로세스를 waitings에 넣기
                continue;
            }
            
            p = waitings.poll();    //  실행될 프로세스
            answer[p.score] += (curTime - p.callTime);
            curTime += p.runtime;
        }
        
        answer[0] = curTime;
        
        return answer;
    }
    
    private static class Process {
        public int score;
        public int callTime;
        public int runtime;
        
        public Process(int score, int callTime, int runtime) {
            this.score = score;
            this.callTime = callTime;
            this.runtime = runtime;
        }
    }
}
```