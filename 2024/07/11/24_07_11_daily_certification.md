# 24_07_11_daily_certification

# Operating System

# Process

## Program vs Process

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Process%201ef9e45eed254e199831b0caa6163829/Untitled.png)

**Program  : Secondary Storage에 있는 실행 가능한 명령어/코드 집합 (Data)**

**Process : Program이   Memory에 적재되어 실행되는 상태, “실행 중인 프로그램”**

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Process%201ef9e45eed254e199831b0caa6163829/Untitled%201.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Process%201ef9e45eed254e199831b0caa6163829/Untitled%202.png)

## Process

- foreground process : 사용자가 볼 수 있는 공간에서 실행되는 프로세스
    - Web Browser, Text Editor, Game, …
- background process : 사용자가 볼 수 없는 공간에서 실행되는 프로세스
    - 사용자와 직접 상호작용이 가능한 백그라운드 프로세스
    - 사용자와 상호작용하지 않고 정해진 일만 수행하는 프로세스, daemon, service

## Process Control Block (PCB)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Process%201ef9e45eed254e199831b0caa6163829/Untitled%203.png)

- 모든 Process는 **실행되기 위해 CPU가 필요하다.**
- CPU 자원은 한정되어 있다.
- Process들은 할당된 시간 만큼 CPU를 이용하고 할당된 시간이 끝나면 CPU를 다른 Process에 양보한다.
    - 할당된 시간이 끝나면 **Timer(Timeout) Interrupt**에 의해 CPU 사용을 중지하고 다른 Process에 양보한다.
- 번갈아가며 실행되는 Process들을 관리하기 위해 PCB를 사용한다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Process%201ef9e45eed254e199831b0caa6163829/Untitled%204.png)

**Program이 실행되어 Process가 Memory에 적재될 때, PCB가 Kernel Space에 생성되고, Process가 종료되면 PCB가 Kernel Space에서 제거됨**

### PCB에 저장되는 정보

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Process%201ef9e45eed254e199831b0caa6163829/Untitled%205.png)

- **Process ID (PID)**
    - Process를 식별하기 위해 부여되는 고유 번호
- **Register Value**
    - Program Counter, Stack Pointer 등등
    - 정해진 시간 만큼 CPU 레지스터를 사용 후 CPU를 양보할 때 그때의 레지스터 중간값  저장
    - 다시 CPU를 할당받아 사용할 때 레지스터 값을 복원하여 실행 재개
    - **Context Switch를 위해 저장**
- **Process State**
- **CPU Scheduling Info**
    - Process가 언제, 얼마나 CPU를 할당받아서 사용할지 관련 정보
- **Memory Info**
    - Process가 저장되어 있는 address에 대한 정보
    - **Page Table**
- **File, I/O Device Info**
    - Process에 할당된 I/O Device
    - 사용 중인(열린) File 정보

## Context Switch

- Q. Process A가 CPU를 할당받아 실행되다가 Timer Interrput가 발생해서 Process B에게 CPU를 양보하여 Process B로 실행 순서가 넘어가는 경우 어떤 일이 발생할까?

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Process%201ef9e45eed254e199831b0caa6163829/Untitled%206.png)

- **Process A는 현재 실행까지의 중간 정보(Context)를 백업한다.**
    - Register(PCB, SP 등), Memory, File, I/O Device
    - 다시 Process A가 CPU를 얻어 실행될 때 이전 실행을 재개하기 위한 정보가 Context임
    - **Process A의 Context를 백업해 두면 후에 다시 Process A를 실행할 수 있음**
- Process A에 이어 실행할 Process B의 Context를 복구한다.
    - 자연스럽게 Process B가 실행된다.
- 위의 과정을 **Context Switching**이라고 한다.
    - 기존 실행 중인 Process의 Context를 저장하고, 새로 실행될 Process의 Context를 복구
    - 여러 Process가 CPU를 번갈아 가며 사용하며 실행되는 원리

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Process%201ef9e45eed254e199831b0caa6163829/Untitled%207.png)

## Process Address Space

- Kernel Space에는 Process의 PCB가 저장된다.
- User Space에는 어떤 정보가 저장될까?

### Process Address Space

![images_gabie0208_post_eaff93ef-af1b-414b-bfe9-ee7bfaa6199f_스크린샷 2021-10-11 오후 5.05.47.png](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Process%201ef9e45eed254e199831b0caa6163829/images_gabie0208_post_eaff93ef-af1b-414b-bfe9-ee7bfaa6199f_%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2021-10-11_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_5.05.47.png)

- **Code (Text)**
    - 실행할 수 있는 코드, **기계어로 이루어진 명령어 저장**
    - 데이터가 아니라 CPU가 실행할 명령어가 저장된 공간으로 **read-only**
    - 정적 할당 영역
- **Data**
    - 프로그램이 실행되는 동안 계속 유지할 데이터 저장
    - 전역 변수 저장 영역
    - 정적 할당 영역
- **Heap**
    - 프로그래머가 프로그램을 사용하며 직접 할당할 수 있는 저장 공간
    - 사용한 메모리 공간은 사용이 완료되면 반환해야 함 (Memory Leak 방지)
    - 동적 할당 영역
- **Stack**
    - 데이터가 일시적으로 저장되는 공간
    - 임시로 사용할 값들을 저장하는 공간
    - 매개 변수, 지역 변수 저장 영역
    - 동적 할당 영역

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Process%201ef9e45eed254e199831b0caa6163829/Untitled%208.png)

## Process State

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Process%201ef9e45eed254e199831b0caa6163829/Untitled%209.png)

OS는 Process State를 PCB에 기록해서 관리한다.

### Process State

![3_02_ProcessState (1).jpg](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Process%201ef9e45eed254e199831b0caa6163829/3_02_ProcessState_(1).jpg)

- **New**
    - Process가 Memory에 적재되어 PCB가 할당된 상태이다.
    - Process가 실행될 준비가 완료되면 Ready 상태로 변경된다.
- **Ready**
    - CPU를 할당받으면 실행될 수 있는 상태이다.
    - 실행될 차례가 아니므로 기다리는 상태이다.
    - Running 상태로 이동하는 것을 Dispatch라고 한다.
- **Running**
    - CPU를 할당받아서 실행 중인 상태이다.
    - 할당된 시간을 모두 사용하면 Timer Interrupt가 발생하여 Ready 상태로 변경된다.
    - 실행 중 I/O 발생 시 Waiting 상태로 이동하여  I/O 작업이 끝날 때 까지(I/O Completion Interrupt를 받을 때까지) 대기한다.
- **Wating (Blocked)**
    - Process가 실행 도중 I/O Device를 사용하는 경우 위치하는 상태이다.
    - I/O 작업은 CPU에 비해 느리므로 Waiting 상태로 대기한다.
    - **I/O 작업이 끝나면(I/O Completion Interrupt를 받으면) Ready 상태로 이동한다.**
- **Terminated**
    - Process가 종료된 상태이다.
    - PCB, Process Address Space를 정리한다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Process%201ef9e45eed254e199831b0caa6163829/Untitled%2010.png)

## Process Hierarchy

Process가 실행되는 도중에 System Call을 통해 다른 Process를 생성할 수 있다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Process%201ef9e45eed254e199831b0caa6163829/Untitled%2011.png)

- **Parent Process** : 새로운 Process를 생성한 기존 Process
- **Child Process** : Parent Process에 의해 생성된 Process
- Parent Process와 Child Process는 별개의 Process이다.
    - **PID가 서로 다르다.**
    - 일부 PCB는 Parent Process의 PID를 PPID(Parent Process ID)로 명시하기도 한다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Process%201ef9e45eed254e199831b0caa6163829/b5ceb6a1-0f7e-4e70-8460-391b19f1357f.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Process%201ef9e45eed254e199831b0caa6163829/Untitled%2012.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Process%201ef9e45eed254e199831b0caa6163829/Untitled%2013.png)

macOS에서는 launchd가 최초의 Process이다.

### System Call

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Process%201ef9e45eed254e199831b0caa6163829/b5ceb6a1-0f7e-4e70-8460-391b19f1357f.png)

- **fork**
    - Parent Process는 fork System Call로 자신의 복사본을 Child Process를 호출한다.
    - Child Process는 Parent Process와 같은 code, data, register 값 등을 가진다.
    - 엄연히 별개의 Process로 PID도 다르고, 할당된 Memory Space도 다르다.
    
    ![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Process%201ef9e45eed254e199831b0caa6163829/Untitled%2014.png)
    
- **exec**
    - fork로 복사된 Child Process는 exec System Call로 자신의 Memory Space를 다른 Program으로 교체한다.
    - Memory Space를 새로운 Program으로 덮어쓴다.
    - Code, Data 영역은 새로 실행될 Program 내용으로 변경되고 나머지 영역은 초기화된다.
        
        ![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Operating%20System%20b21179dcea4b48edb29c7a0d86c077e6/Process%201ef9e45eed254e199831b0caa6163829/Untitled%2015.png)
        
        - Login Process는 fork System Call로 복제본인 Child Process를 생성한다.
        - 복제본 Child Process는 exec System Call로 bash Process로 교체한다.