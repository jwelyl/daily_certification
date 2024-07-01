# 24_07_01_daily_certification

# Computer Architecture

# CPU

![cpu.jpeg](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/cpu.jpeg)

# ALU

## ALU

![alu.jpeg](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/alu.jpeg)

## Flag Register

![flag_register.jpeg](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/flag_register.jpeg)

## Control Unit

![control_unit.jpeg](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/control_unit.jpeg)

# Register

![cpu.jpeg](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/cpu%201.jpeg)

1. **Program Counter(PC)**
    
    메모리에서 가져올 명령어의 주소 **(메모리에서 읽어 들일 명령어의 주소)**
    
2. **Instruction Register(IR)**
    
    해석할 명령어 **(방금 메모리에서 읽어 들인 명령어)**
    
3. **Memory Address Register(MAR)**
    
    메모리의 주소 (CPU가 주소 버스로 내보낼 주소) 
    
4. **Memory Buffer Register(MBR)**
    
    메모리와 주고 받을 값 (데이터, 명령어)
    
    ![pc.jpeg](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/pc.jpeg)
    
    Program Counter가 순차적으로 증가하지 않는 경우
    
    - JUMP, CONDITIONAL JUMP, CALL, RET
    - Interrupt
    - etc…
    
5. **Flag Register**
    
    연산 결과, CPU 상태에 대한 부가 정보
    
6. **General Purpose Registerㄴ**
    
    다양하고 일반적인 상황에서 자유롭게 사용
    
7. **Stack Pointer**
    - **스택 주소 지정 방식에 사용**
    - 스택의 꼭대기를 가리키는 레지스터
        
        ![stack.jpeg](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/stack.jpeg)
        
8. **Base Register**
    - **변위 주소 지정 방식에 사용**
        
        ![displacement.jpeg](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/displacement.jpeg)
        
    - **operand** 필드 값(변위)과 특정 레지스터 값을 더하여 유효 주소 얻기
        - 상대 주소 지정 방식 (Program Counter)
            
            ![pc_addressing.jpeg](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/pc_addressing.jpeg)
            
        - 베이스 레지스터 주소 지정 방식 (Base Register)
            
            ![base_addressing.jpeg](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/base_addressing.jpeg)
            
        

# Instruction Cycle

![instruction_cycle_interrupt.jpeg](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/instruction_cycle_interrupt.jpeg)

**프로그램의 명령어들은 일정 주기마다 실행**되는데 이 주기를 명령어 사이클이라고 한다.

기본적으로는 인출-실행-인출-실행-…이 반복된다.

![fetch_excute.jpeg](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/fetch_excute.jpeg)

- 인출 사이클 : 명령어를 CPU로 가져온다.
- 실행 사이클 : CPU로 가져온 명령어를 실행한다.

어떤 명령어의 경우 추가적인 메모리 접근이 더 필요한데 이 경우 간접 사이클이 추가된다.

![indirect_cycle.jpeg](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/indirect_cycle.jpeg)

# Interrupt

**CPU가 우선적으로 처리해야 할 다른 작업이 생겼을 때 발생한다.**

![Untitled](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/Untitled.png)

![Untitled](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/4e3efc4a-4cfb-45a2-9d69-48a206127577.png)

- **동기 인터럽트 (Exception)**
- **비동기 인터럽트 (Hardware Interrupt)**

## Synchronous Interrupt (Exception)

**CPU가 프로그램 실행 중 예기치 못한 상황을 접했을 때(0으로 나눈다던지, 잘못된 메모리에 접근한다던지) 발생한다.**

![Untitled](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/Untitled%201.png)

![Untitled](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/Untitled%202.png)

**실행을 멈추고 예외 처리를 한다.**

## Asynchronous Interrupt (Hardware Interrupt)

**주로 I/O Device에 의해 발생하는 인터럽트이다.**

입출력 완료, 키보드 입력 등 H/W적인 알림과 같은 역할을 한다.

![Untitled](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/Untitled%203.png)

![Untitled](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/Untitled%204.png)

**I/O 작업 중에도 효율적으로 명령어를 처리하기 위해 필요하다.**

![Untitled](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/Untitled%205.png)

- I/O 작업은 CPU에 비해 매우 느리다.
- Interrupt가 없다면, CPU는 I/O Device를 매번 확인해서 I/O 작업이 완료되었는지 확인해야 한다.
- Interrupt가 있으므로 I/O Device가 I/O 작업 완료 Interrupt를 보낼 때까지 CPU는 다른 일을 할 수 있다.

## Interrupt 처리 순서

![Untitled](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/Untitled%206.png)

- **Interrupt 요청 신호**
    
    ![Untitled](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/Untitled%207.png)
    
- **Interrupt Flag**
    
    ![Untitled](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/Untitled%208.png)
    
    Interrupt Flag를 통해 Interrupt를 받아들일지 결정한다.
    
    ![Untitled](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/Untitled%209.png)
    
    하지만 Interrupt Flag와 무관하게 막을 수 없는 Interrupt도 존재한다. (H/W 고장 등)
    
- **Interrupt Service Routine**
    
    인터럽트를 받아들였을 때, 해당 인터럽트를 처리하기 위해 실행할 프로그램 
    
    ![Untitled](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/4601431b-1462-47e8-9114-06ef21e62e78.png)
    
    ![interrupt_service_routine.jpeg](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/interrupt_service_routine.jpeg)
    
- **Interrupt Vector**
    
    각각의 인터럽트를 구분하기 위한 정보
    
    인터럽트 서비스 루틴의 시작 주소를 알 수 있다.
    
    ![Untitled](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/Untitled%2010.png)
    
    Interrupt Vector Table
    

**CPU가 인터럽트를 처리한다는 것은, 인터럽트 서비스 루틴을 처리하고, 원래 수행하던 작업으로 돌아온다.**

![Untitled](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/Untitled%2011.png)

인터럽트 서비스 루틴으로 넘어가기 전에 CPU 레지스터 값들을 백업해두어야 한다.

![Untitled](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/Untitled%2012.png)

해당 값들을 스택 영역에 저장해둔다.

![Untitled](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/Untitled%2011.png)

인터럽트 서비스 루틴 종료 후 해당 값들을 다시 복구한다.

![instruction_cycle.jpeg](24_07_01_daily_certification%20a2cce22a5b7341f49ef26d55ea31d42d/instruction_cycle.jpeg)

# Problem Solving (Algorithm & SQL)

### BOJ 10423 전기가 부족해

[](https://www.acmicpc.net/problem/10423)

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer tokens;
    
    private static int N;    //    도시 개수
    private static int M;    //    케이블 개수
    private static int K;    //    발전소 개수
    
    private static boolean[] connected;       //    connected[i] : 도시 i가 전력 공급되었으면 true
    private static boolean[][] usedCables;    //    usedCable[i][j] : 두 도시 i, j를 연결하는 케이블이 이미 pq에 들어갔으면 true
    
    private static List<Edge>[] graph;
    private static final PriorityQueue<Edge> pq = new PriorityQueue<>();
    
    private static int remain = 0;    //    아직 전력 공급 되지 않은 도시 수
    private static int ans = 0;       //    모든 도시가 전력 공급 되기 위해 필요한 케이블 최소 비용
    
    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        N = Integer.parseInt(tokens.nextToken());
        M = Integer.parseInt(tokens.nextToken());
        K = Integer.parseInt(tokens.nextToken());
    
        remain = N - K;
        
        connected = new boolean[N + 1];
        usedCables = new boolean[N + 1][N + 1];
        
        tokens = new StringTokenizer(br.readLine());
        for(int i = 0; i < K; i++)
            connected[Integer.parseInt(tokens.nextToken())] = true;    //    발전소 설치된 도시
        
        graph = new ArrayList[N + 1];
        for(int i = 1; i <= N; i++)
            graph[i] = new ArrayList<Edge>();
        
        for(int i = 0; i < M; i++) {
            tokens = new StringTokenizer(br.readLine());
            int u = Integer.parseInt(tokens.nextToken());
            int v = Integer.parseInt(tokens.nextToken());
            int w = Integer.parseInt(tokens.nextToken());
            
            if(connected[u] && connected[v])
                continue;    //    이미 연결된 두 발전소끼리 연결할 필요 없음
            
            Edge e1 = new Edge(v, w);
            Edge e2 = new Edge(u, w);
            graph[u].add(e1);
            graph[v].add(e2);
            
            if(connected[u]) {
                usedCables[u][v] = true;
                usedCables[v][u] = true;
                pq.offer(e1);
            }
            else if(connected[v]) {
                usedCables[u][v] = true;
                usedCables[v][u] = true;
                pq.offer(e2);
            }
        }
        
        while(!pq.isEmpty()) {
            Edge minEdge = pq.poll();
            int v = minEdge.vertex;
            int c = minEdge.cost;
            
            if(connected[v])    //    이미 연결된 도시면 skip
                continue;
            
            connected[v] = true;
            ans += c;
            remain--;
            
            if(remain == 0)
                break;
            
            for(Edge edge : graph[v]) {
                int nv = edge.vertex;
                
                if(usedCables[v][nv])    //    이미 pq에 들어왔던 간선일 경우
                    continue;
            
                pq.offer(edge);
            }
        }
        
        System.out.println(ans);
    }   // main-end
    
    private static class Edge implements Comparable<Edge> {
        int vertex;
        int cost;
        
        public Edge(int vertex, int cost) {
            this.vertex = vertex;
            this.cost = cost;
        }
        
        @Override
        public int compareTo(Edge other) {
            return Integer.compare(this.cost, other.cost);
        }
    }
}   //  Main-class-end
```