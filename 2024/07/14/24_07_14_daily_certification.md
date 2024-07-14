# 24_07_14_daily_certification

# Operating System

# CPU Scheduling

## CPU Scheduling

- **OS가 Process에게 CPU를 공정하고 합리적으로 재분배하는것**
- **어떻게 CPU를 분배하는 것이 공정하게 분배하는 것일까?**
    - 무조건 똑같이 분배하는게 좋을까? → NO!!
    - **Process마다 Priority가 존재한다.**

## Process Priority

- **Process Priority는 PCB에 저장되어 있다.**
    
    ![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/CPU%20Scheduling%200eb06e34a02543bd90c09b62ff8f0489/Untitled.png)
    
- **I/O Bound Process’ Priority > CPU Bound Process’s Priority**
    
    ![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/CPU%20Scheduling%200eb06e34a02543bd90c09b62ff8f0489/Untitled%201.png)
    
    - I/O Bound Process는 대부분 Waiting State에 존재하므로 CPU의 우선 순위를 높여서 빠르게 CPU를 사용하게 해주고 Waiting State에 돌입해 CPU Bound Process가 CPU를 집중적으로 사용할 수 있게 하는게 유리하다.
- linux/macOS에서 ps -el 명령어로 Process Priority도 확인 가능하다.
    
    ![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/CPU%20Scheduling%200eb06e34a02543bd90c09b62ff8f0489/Untitled%202.png)
    

## Scheduling Queue

- OS가 모든 Process의 PCB를 일일히 확인하면서 Priority를 확인할 수는 없다.
- **Process들은 Scheduling Queue에서 관리한다.**
    
    ![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/CPU%20Scheduling%200eb06e34a02543bd90c09b62ff8f0489/Untitled%203.png)
    
- **Scheduling Queue는 크게 Ready Queue와 Waiting Queue로 나뉜다.**
    
    ![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/CPU%20Scheduling%200eb06e34a02543bd90c09b62ff8f0489/Untitled%204.png)
    
    ![3_02_ProcessState (1).jpg](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/CPU%20Scheduling%200eb06e34a02543bd90c09b62ff8f0489/3_02_ProcessState_(1).jpg)
    
    - **Ready Queue : CPU를 사용하기 위해 Process가 대기하는 Queue**
    - **Waiting Queue : I/O 작업을 위해 Process가 대기하는 Queue**
        
        ![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/CPU%20Scheduling%200eb06e34a02543bd90c09b62ff8f0489/Untitled%205.png)
        
    
    ![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/CPU%20Scheduling%200eb06e34a02543bd90c09b62ff8f0489/Untitled%206.png)
    

## Preemptive/Non-preemptive Scheduling

### Preemptive Scheduling

현재 CPU를 사용 중인 Process가 종료되거나 Waiting 상태가 되지 않아도 다른 Process가 CPU를 차지하여 사용할 수 있는 Scheduling

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/CPU%20Scheduling%200eb06e34a02543bd90c09b62ff8f0489/Untitled%207.png)

- Process의 CPU 독점을 막을 수 있어서 모든 Process가 CPU를 골고루 사용할 수 있다.
- Context Switch 오버헤드가 더 커진다.

### Non-preemptive Scheduling

현재 CPU를 사용 중인 Process가 종료되거나 Waiting 상태가 되지 않으면 다른 Process가 CPU를 차지하여 사용할 수 없는 Scheduling

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/CPU%20Scheduling%200eb06e34a02543bd90c09b62ff8f0489/Untitled%208.png)

- Context Switch 오버헤드가 적다.
- Process의 CPU 독점을 막을 수 없어서 모든 Process가 CPU를 골고루 사용할 수 없다.

## CPU Scheduling Algorithm

### FCFS (First Come First Served)

- Ready Queue에 삽입된 순서대로 처리하는 Scheduling
- Non-preemptive Scheduling
    - 먼저 삽입된 Process가 끝날 때 까지 CPU를 양보하지 않는다.
- 먼저 CPU를 요청한 Process부터 CPU를 할당받아 실행된다.
- Process들이 기다리는 시간이 매우 길어질 수 있다는 단점 (Convoy Effect)

![fcfs.png](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/CPU%20Scheduling%200eb06e34a02543bd90c09b62ff8f0489/fcfs.png)

먼저 삽입된 Process의 실행 시간이 길어질 수록, 후에 삽입된 Process의 대기 시간이 길어진다.

### SJF (Shortest Job First)

- Convoy Effect 방지를 위한 Scheduling
- CPU 사용 시간이 짧은 Process가 가장 먼저, CPU 사용 시간이 긴 Process가 가장 나중에 실행된다.
- Non-preemptive Scheduling

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/CPU%20Scheduling%200eb06e34a02543bd90c09b62ff8f0489/Untitled%209.png)

### RR (Round Robin)

- FCFS + time slice
- time slice : 각 Process가 CPU를 사용할 수 있도록 정해진 시간
- 정해진 time slice만큼의 시간 동안 CPU를 사용하는 Preemptive Scheduling
    - 먼저 Ready Queue에 들어온 Process부터 CPU를 사용하지만 time slice만큼만 CPU를 사용함
    - time slice가 지난 이후, 실행 중인 Process는 실행을 멈추고 Ready Queue의 가장 뒤에 삽입
    - **time slice마다 Contex Switch**

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/CPU%20Scheduling%200eb06e34a02543bd90c09b62ff8f0489/Untitled%2010.png)

- 적절한 time slice의 크기가 매우 중요하다
    - time slice가 너무 클 경우 : FCFS처럼 Convoy Effect가 발생하여, Process의 response time이 커진다.
    - time slice가 너무 작을 경우 : 너무 빈번한 Context Switch로 Overhead가 커진다.

### SRT (Shortest Remaining Time)

- SJF + RR
- Process가 time slice만큼 CPU를 사용한다. (Round Robin)
- 다음에 실행할 Process는 잔여 실행 시간이 가장 짧은 Process가 실행된다. (Shortest Job First)

### Priority Scheduling

- Process에 부여된 Priority를 고려하여, Priority가 높은 Process부터 실행된다.
- 같은 Priority의 Process가 여러 개일 경우, FCFS로 먼저 삽입된 Process부터 실행한다.
- **Starvation**
    - Priority가 높은 Process가 CPU를 독점하여 실행된다.
    - Priority가 낮은 Process는 먼저 Ready Queue에 삽입되었어도 실행될 수 없다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/CPU%20Scheduling%200eb06e34a02543bd90c09b62ff8f0489/Untitled%2011.png)

- **Aging**
    - Starvation 해결 기법
    - 일정 시간마다 Ready Queue에서 대기 중인 Process들의 Priority를 점차 증가시킨다.
    - 언젠가는 실행 중인 Process보다 Priority가 높아져서 실행되게 된다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/CPU%20Scheduling%200eb06e34a02543bd90c09b62ff8f0489/Untitled%2012.png)

### MLQ (Multilevel Queue)

- Priority Scheduling의 발전된 형태
- Priority마다 Ready Queue를 사용한다.
    - 가장 높은 Priority의 Ready Queue에 있는 Process를 먼저 실행한다.
    - 가장 높은 Priority의 Ready Queue가 비어있으면 그 다음 Ready Queue의 Process를 실행한다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/CPU%20Scheduling%200eb06e34a02543bd90c09b62ff8f0489/Untitled%2013.png)

- Ready Queue마다 서로 다른 Scheduling Algorithm을 적용할 수 있다.
- **Starvation**
    - 낮은 Priority의 Ready Queue의 Process는 높은 Priority의 Ready Queue로 이동할 수 없다.

### MLFQ (Multilevel Feedback Queue)

- MLQ의 발전된 형태
- Process가 Priority에 따라 Ready Queue 사이를 이동할 수 있다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/CPU%20Scheduling%200eb06e34a02543bd90c09b62ff8f0489/Untitled%2014.png)

- 최초에 Ready 상태인 Process는 가장 Priority가 높은 Ready Queue에 삽입되어 먼저 실행된다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/CPU%20Scheduling%200eb06e34a02543bd90c09b62ff8f0489/Untitled%2015.png)

- time slice만큼 Process를 실행시키고 Priority가 낮은 Ready Queue로 이동시킨다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/CPU%20Scheduling%200eb06e34a02543bd90c09b62ff8f0489/Untitled%2016.png)

- aging 기업을 적용해 Priority가 낮은 Ready Queue의 Process를 Priority가 높은 Ready Queue로 이동시킬 수 있다.
- **Process의 CPU 사용 시간이 길 경우 Priority가 낮아진다.**
- **Priority가 낮은 Process의 경우 aging을 통해 Priority를 높여 Starvation을 방지한다.**