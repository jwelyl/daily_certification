# 24_07_13_daily_certification

# Operating System

# Thread

## Thread

- 하나의 Process를 구성하는 실행 흐름의 단위
- 하나의 Process는 여러 개의 Threa를 가질 수 있다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Thread%2058797be739ff49a29fe998e6a581dcc7/Untitled.png)

기존에 설명했던 Process는 **Single Thread Process**였다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Thread%2058797be739ff49a29fe998e6a581dcc7/Untitled%201.png)

하지만 실제 Process는 **Multi Thread Process**이다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Thread%2058797be739ff49a29fe998e6a581dcc7/Untitled%202.png)

워드 프로세서의 경우 사용자로부터 입력을 받아 처리하는 thread와 입력된 내용을 자동 저장하는 thread가 존재하며 두 thread가 동시에 실행될 수 있다.

**어떻게 여러 Thread가 동시에 실행될 수 있는가?**

## Thead 구성 요소

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Thread%2058797be739ff49a29fe998e6a581dcc7/Untitled%203.png)

Thread가 실행되는데 필요한 최소한의 정보를 가지고 있다.

- **Thread ID**
- **Register Value (PC, SP, …)**
- **Stack**

**모든 Thread는 Process의 자원을 공유하여 사용한다.**

- 여러 Thread가 Process의 Code, Data를 공유한다.
- Process에서 사용하는 File에 Thread들이 접근 가능하다.
- etc

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Thread%2058797be739ff49a29fe998e6a581dcc7/Untitled%204.png)

최근에는 Process와 Thread의 구분이 모호해지고 있다. Linux에서는 둘을 구분하지 않고 Task라 한다.

최근 OS에서는 CPU에 처리해야 할 작업을 전달할 때, Process 단위가 아니라 Thread 단위로 전달한다.

## Multi-Process, Multi-Thread

- **동일한 작업을 수행하는 Single Thread Process 여러 개 실행**
- **하나의 Process에 여러 개의 Thread가 존재하여 실행**

둘의 차이는?

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Thread%2058797be739ff49a29fe998e6a581dcc7/Untitled%205.png)

둘 다 “hello, os”를 3번 출력한다.

하지만 fork로 생성된 3개의 Process는 서로 다른 Process로 자원을 공유하지 않고 복제하여 각각 존재한다.

**Multi-Process**

- Process fork 시 code/data/heap space 등 모든 자원이 복제되어 저장된다.
- 적재된 Memory Address를 제외한 모든 것이 동일한 Process 3개가 Memory에 적재된다.
- PCB가 여러 개 존재한다.
- Process끼리는 자원을 공유하지 않으므로 통신이 까다롭다. (IPC)
- Inter-Process Communication (IPC)
    - File
    - Pipe
    - Socket
    - Shared Memory
    - Message Queue

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Thread%2058797be739ff49a29fe998e6a581dcc7/Untitled%206.png)

**Multi-Thread**

- 여러 Thread들은 Thread ID, Register, Stack만을 독립적으로 가진다. (별도 실행을 위한 최소 정보)
- 그 외에는 Process의 자원을 여러 Thread가 공유한다.
- 하나의 PCB가 존재한다.
- Thread끼리는 자원을 공유하므로 통신에 유리하다. (동시성 문제 발생 가능한 단점도 존재)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Thread%2058797be739ff49a29fe998e6a581dcc7/Untitled%207.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Thread%2058797be739ff49a29fe998e6a581dcc7/Untitled%208.png)