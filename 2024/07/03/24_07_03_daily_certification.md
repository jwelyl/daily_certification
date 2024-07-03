# 24_07_03_daily_certification

# Computer Architecture

# CPU 성능 향상 기법

- Clock 속도를 높이는 방법
- Core 수를 늘리는 방법 (Dual Core, Multicore)
- Thread 수를 늘리는 방법 (Multitrhead)

# Clock

![Untitled](24_07_03_daily_certification%2068b51a76af4746c9a6e0b14ce7a3a978/Untitled.png)

Clock 속도를 높이면 CPU 속도가 빨라질까?

**일반적으로 Clock 속도가 높은 CPU가 성능이 좋기는 하다.**

하지만 Clock 속도를 무한정 높일 수는 없다. 발열이 심해진다.

# Core

![Untitled](24_07_03_daily_certification%2068b51a76af4746c9a6e0b14ce7a3a978/Untitled.png)

위 CPU는 Core가 8개이다. Core란 뭔가?

## 현대적인 관점에서의 CPU

![cpu.jpeg](24_07_03_daily_certification%2068b51a76af4746c9a6e0b14ce7a3a978/cpu.jpeg)

- CPU에는 ‘**명령어를 실행하는 부품’**이 여러 개 존재한다.
- CPU 내부에 ‘**명령어를 실행하는 부품’이 코어**이다.
- **CPU(Processor) 안에 Core가 여러 개 존재 가능하다.**
- 전통적인 CPU는 core가 1개로 single-core processor라고 한다.
- **현대적인 CPU는 core가 2개 이상으로 multi-core processor라고 한다.**

## Core 수와 CPU 속도는 정비례하는가?

**그렇지 않다.** 

적당한 명령어 수에 맞는 적당한 core 수는 속도 증가를 기대할 수 있지만, 무작정 속도가 증가하지는 않는다.

# Thread

- **실행 흐름의 단위**
- Hardware Thread
- Software Thread

![thread.png](24_07_03_daily_certification%2068b51a76af4746c9a6e0b14ce7a3a978/thread.png)

## Hardware Thread

**하나의 코어가 동시에 처리하는 명령어 단위**

**1 core 1 thread CPU**

![1core1thread.jpeg](24_07_03_daily_certification%2068b51a76af4746c9a6e0b14ce7a3a978/1core1thread.jpeg)

CPUI의 1개의 core가 하나의 명령어를 받아들여서 한번에 하나의 명령어를 처리할  수 있다.

**2 core 4 thread CPU**

![2core4thread.jpeg](24_07_03_daily_certification%2068b51a76af4746c9a6e0b14ce7a3a978/2core4thread.jpeg)

CPU에 명령어를 받는 2개의 core가 있고, 각 core마다 명령어를 한번에 2개씩 실행할 수 있으면, CPU는 한 번에 4개의 명령어를 처리할 수 있다.

이렇게 하나의 core가 여러 개의 명령어를 동시에 처리할 수 있는 CPU를 **Multithread Processor, Multithread CPU**라고 한다.

![Untitled](24_07_03_daily_certification%2068b51a76af4746c9a6e0b14ce7a3a978/Untitled.png)

위 CPU는 명령어를 처리하는 core가 8개고, 각 core마다 동시에 2개의 명령어를 처리 가능하여 CPU가 총 16개의 명령어를 동시 처리 가능하다. (Multi-thread)

**Multithread Processor에서는 레지스터가 핵심이다.**

![hwthread_multi.jpeg](24_07_03_daily_certification%2068b51a76af4746c9a6e0b14ce7a3a978/hwthread_multi.jpeg)

core에 register set(PC, IR, SP, BR 등등 Instruction 실행 위한 필수 레지스터 집합)이 여러 개일 경우, 해당 core는 여러 개의 명령어를 동시에 처리 가능해서 Multithreading이 가능하다. 

![hwthread_program.jpeg](24_07_03_daily_certification%2068b51a76af4746c9a6e0b14ce7a3a978/hwthread_program.jpeg)

Program이 CPU가 몇 core, 몇 thread인지는 알지 못한다.

H/W Thread 개수는 S/W 입장에서 논리적인 작업 흐름 개수와 같다.

**H/W Thread를 Logical Processor라고도 한다.**

## Software Thread

**하나의 프로그램에서 독립적으로 실행되는 단위**

![swthread.jpeg](24_07_03_daily_certification%2068b51a76af4746c9a6e0b14ce7a3a978/swthread.jpeg)

Word Processor 프로그램의 경우, 1. 사용자로부터 입력받은 내용을 화면에 보여주기도 해야하고, 2. 사용자가 입력한 내용이 맞춤법에 맞는지도 확인해야 하고, 3. 사용자가 입력한 내용을 수시로 저장도 해야한다.

만약 Single Thread Program이라면, 1, 2, 3은 동시에 이루어질 수 없고 순차적으로 이루어져야 한다. 입력을 받는 동안 수시로 저장할 수도 없고, 맞춤법 검사도 이루어지지 않는다.

![swmultitrhead.jpeg](24_07_03_daily_certification%2068b51a76af4746c9a6e0b14ce7a3a978/swmultitrhead.jpeg)

Multi Thread Program이면 1, 2, 3은 동시에 이루어질 수 있다. 입력 중에도 맞춤법 검사가 이루어질 것이고, 수시로 저장도 가능할 것이다.

**H/W Thread와 S/W Thread는 관계가 없다.**

1 core 1 thread CPU도 여러 개의 S/W Thread를 만들 수 있다.

# Instruction Pipeline

## 명령어 처리 과정

1. 명령어 인출 (Instruction Fetch)
2. 명령어 해석 (Instruction Decode)
3. 명령어 실행 (Execute Instruction)
4. 결과 저장 (Write Back)

## Instruction Pipeline

**동시에 같은 단계가 겹치지만 않는다면 CPU는 각 단계를 동시에 실행할 수 있다.**

명령어 1을 decode하는 동안, 명령어 2를 fetch할 수 있다. OK!

명령어 2를 decode하는 동안, 명령어 3을 decode할 수 있다. NO!!

![pipeline.jpeg](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/CPU%20Performance%20e2333d5b6b2948749ff49b0710879c0a/pipeline.jpeg)

t3의 경우 instruction1, 2, 3, 4의 서로 다른 과정이 동시에 실행된다.

명령어 파이프라인이 없을 경우

![withoutpipeline.jpeg](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/CPU%20Performance%20e2333d5b6b2948749ff49b0710879c0a/withoutpipeline.jpeg)

하나의 명령어의 모든 과정이 끝난 뒤에야 다른 명령어가 실행될 수 있다.

## Pipeline Hazard

**명령어 파이프라인이 항상 성능을 향상 시키는 것은 아니다.**

- **Data Hazard**
- **Control Hazard**
- **Structural Hazard**

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/CPU%20Performance%20e2333d5b6b2948749ff49b0710879c0a/Untitled.png)

### Data Hazard

명령어 간의 데이터의 의존성에 의해 야기된다.

```
Instruction 1 : R1 <- R2 + R3  //  R2 레지스터 값과 R3 레지스터 값을 더한 값을 R1 레지스터에 저장
Instruction 2 : R4 <- R1 + R5  //  R1 레지스터 값과 R5 레지스터 값을 더한 값을 R5 레지스터에 저장
```

Instruction 1이 완전히 실행되어 R1의 값이 계산되고 저장된 뒤에 Instruction 2에서 R1의 값을 가져올 수 있다.

### Control Hazard

Program Counter가 순차적으로 증가하지 않고 갑작스럽게 변화하여 발생한다.

![control_hazard.png](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/CPU%20Performance%20e2333d5b6b2948749ff49b0710879c0a/control_hazard.png)

### Structural Hazard

서로 다른 Instruction이 같은 CPU 부품(ALU, Register, …)을 사용하려고 할 때 발생한다.

## Super Scalar

CPU 내부에 여러 개의 Instruction Pipleline을 포함한 구조 (Multithread Processor)

![superscalar.jpeg](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/CPU%20Performance%20e2333d5b6b2948749ff49b0710879c0a/superscalar.jpeg)

이론적으로는 Pipeline 개수에 비례해서 성능이 증가한다.

하지만 Pipeline Hazard도 증가해서 실제로 성능이 정비례하지는 않는다.

## Out Of Order Excecution (OOOE)

Pipeline 중단을 방지하기 위해 Instruction을 순차적으로 처리하지 않는 명령어 병렬 처리 기법

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/CPU%20Performance%20e2333d5b6b2948749ff49b0710879c0a/Untitled%201.png)

위의 명령어들을 순차적으로 실행할 경우

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/CPU%20Performance%20e2333d5b6b2948749ff49b0710879c0a/Untitled%202.png)

 3번 명령어는 1, 2번 명령어가 완전히 실행될 때까지 기다려야 하므로 pipeline 중단 발생

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/CPU%20Performance%20e2333d5b6b2948749ff49b0710879c0a/Untitled%203.png)

의존성이 없는 명령어들을 먼저 실행하도록 순서를 변경하면(3번 명령어를 가장 나중에 실행)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/CPU%20Performance%20e2333d5b6b2948749ff49b0710879c0a/Untitled%204.png)

pipeline 중단이 발생하지 않는다.

아무 명령어나 순서를 변경할 수는 없다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/CPU%20Performance%20e2333d5b6b2948749ff49b0710879c0a/Untitled%205.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/CPU%20Performance%20e2333d5b6b2948749ff49b0710879c0a/Untitled%206.png)

# Instruction Set Architecture (ISA)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/CPU%20Performance%20e2333d5b6b2948749ff49b0710879c0a/Untitled%207.png)

- **CPU가 이해할 수 있는 명령어들의 모음**
- CPU 종류에 따라 명령어 집합이 다르다.
    - Intel CPU 컴퓨터에서 만든 프로그램 실행 파일을 M3 CPU 사용하는 mac 컴퓨터에서 실행시키려 하면 실행이안된다.
    - 같은 C 프로그램도 x86-64이냐 ARM이냐에 따라 컴파일된 Assembly 코드가 다르다.
- 명령어 집합이 달라지면 해석 방식, 레지스터 종류, 개수, 파이프라이닝 등등 많은 것이 달라진다.

# CISC, RISC

## CISC (Complex Instruction Set Computer)

### 특징

- 복잡하고 다양한 명령어 집합을 활용하는 컴퓨터 (CPU)
- x86, x86-64, Intel, AMD
- 명령어의 형태, 크기가 다양한 **가변 길이 명령어 활용**

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/CPU%20Performance%20e2333d5b6b2948749ff49b0710879c0a/Untitled%208.png)

- 다양한 명령어 하나하나가 강력하다.
- 상대적으로 적은 수의 명령어로도 프로그래밍이 가능하다.
- 메모리를 적게 사용한다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/CPU%20Performance%20e2333d5b6b2948749ff49b0710879c0a/Untitled%209.png)

### **단점**

- **명령어 파이프라이닝이 불리하다.**
- 명령어의 크기와 실행되기까지의 시간이 일정하지 않다.
    - 클럭 주기가 일정하지 않고, 복잡한 명령어는 여러 클럭 주기가 필요하다.
- 대다수의 복잡한 명령어는 사용 빈도가 낮다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/CPU%20Performance%20e2333d5b6b2948749ff49b0710879c0a/Untitled%2010.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/CPU%20Performance%20e2333d5b6b2948749ff49b0710879c0a/Untitled%2011.png)

## RISC (Reduced Instruction Set Computer)

### 특징

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/CPU%20Performance%20e2333d5b6b2948749ff49b0710879c0a/Untitled%2012.png)

- 명령어 종류가 적고, 짧고 규격화된 명령어를 사용한다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/CPU%20Performance%20e2333d5b6b2948749ff49b0710879c0a/Untitled%2013.png)

- 명령어 파이프라이닝에 유리하다.
- ARM
- 메모리 접근 (load, store) 명령어를 최소화하고 더 많은 범용 레지스터로 레지스터 활용을 최대화한다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/CPU%20Performance%20e2333d5b6b2948749ff49b0710879c0a/Untitled%2014.png)

- CISC보다 명령어 종류는 적지만 같은 소스 코드를 컴파일 했을 때 더 많은 명령어를 사용한다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/CPU%20Performance%20e2333d5b6b2948749ff49b0710879c0a/Untitled%2015.png)

# Problem Solving (Algorithm & SQL)

### BOJ 31230 모비스터디

[](https://www.acmicpc.net/problem/31230)

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final long INF = Long.MAX_VALUE;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer tokens;
    
    private static int N;    //    정점 개수
    private static int M;    //    간선 개수
    private static int A;    //    출발지 번호
    private static int B;    //    도착지 번호

    private static List<Node>[] graph;
    private static List<Integer>[] prevs;
    private static long[] dist;
    private static boolean[] visited;
    private static int cnt = 0;
    
    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        N = Integer.parseInt(tokens.nextToken());
        M = Integer.parseInt(tokens.nextToken());
        A = Integer.parseInt(tokens.nextToken());
        B = Integer.parseInt(tokens.nextToken());
        
        graph = new ArrayList[N + 1];
        prevs = new ArrayList[N + 1];
        for(int i = 0; i <= N; i++) {
            graph[i] = new ArrayList<>();
            prevs[i] = new ArrayList<>();
        }
        dist = new long[N + 1];
        visited = new boolean[N + 1];
        Arrays.fill(dist, INF);
        
        for(int i = 0; i < M; i++) {
            tokens = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(tokens.nextToken());
            int v2 = Integer.parseInt(tokens.nextToken());
            long c = Integer.parseInt(tokens.nextToken());
            
            graph[v1].add(new Node(v2, c));
            graph[v2].add(new Node(v1, c));
        }
        
        dijkstra();
        
        sb.append(cnt).append("\n");
        for(int i = 1; i <= N; i++) {
            if(visited[i])
                sb.append(i).append(" ");
        }
        
        System.out.print(sb);
    }   //  main-end
    
    private static void dijkstra() {
        PriorityQueue<Node> pq = new PriorityQueue<>();
        dist[A] = 0;
        pq.offer(new Node(A, dist[A]));
        
        while(!pq.isEmpty()) {
            Node cur = pq.poll();
            int cv = cur.vertex;
            long cc = cur.cost;
            
            if(dist[cv] < cc)
                continue;
            
            for(Node next : graph[cv]) {
                int nv = next.vertex;
                long nc = cc + next.cost;
                
                if(nc < dist[nv]) {
                    dist[nv] = nc;
                    prevs[nv].clear();
                    prevs[nv].add(cv);
                    pq.offer(new Node(nv, dist[nv]));
                }
                else if(nc == dist[nv])
                    prevs[nv].add(cv);
            }
        }
        
        dfs(B);
    }
    
    private static void dfs(int v) {
        visited[v] = true;
        cnt++;
        
        if(v == A)
            return;
        
        for(int nv : prevs[v]) {
            if(!visited[nv])
                dfs(nv);
        }
    }
    
    private static class Node implements Comparable<Node> {
        public int vertex;
        public long cost;
        
        public Node(int vertex, long cost) {
            this.vertex = vertex;
            this.cost = cost;
        }
        
        @Override
        public int compareTo(Node other) {
            return Long.compare(this.cost, other.cost);
        }
    }
}   //  Main-class-end
```