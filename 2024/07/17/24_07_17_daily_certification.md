# 24_07_17_daily_certification

# Operating System

## Kernel

- OS에서 User Application이 H/W, S/W Resource에 접근/조작하는 것을 관리한다.
- User Application이 올바르게 실행되도록 관리한다.
- 자원 접근, 조작 등은 Kernel을 통해서만 가능하고 이러한 것을 Kernel Code를 실행한다고 한다.
- OS의 핵심으로 좁은 의미로 OS라고 하면 Kernel을 의미한다.

## Dual Mode

### User Mode

- User Application이 실행되는 모드
- 운영체제 서비스를 제공받을 수 없다.
- Kernel Code를 실행할 수 없다.

### Kernel Mode

- 운영체제 서비스를 제공받을 수 있는 모드이다.
- Kernel Code를 실행할 수 있는 모드이다.
- CPU가 Kernel Mode일 경우, 자원 접근을 비롯한 모든 코드를 실행할 수 있다.
- User Mode로 실행 중인 프로그램이 자원 접근이 필요할 경우 Kernel Mode로 전환해야 한다.

### System Call

- User Mode로 실행 중인 프로그램이 자원 접근이 필요할 경우 Kernel Mode로 전환해야 한다.
- Kernel Mode로 전환하여 Kernel Code를 실행하기 위해 보내는 요청이 System Call이다.
- System Call을 통해 User Mode에서 Kernel Mode로 전환 후 OS의 서비스를 제공받는다.
- Software Interrupt의 일종이다.
- System Call 호출 시
    - User Application를 실행 중인 CPU의 기존 작업을 백업한다.
    - Kernel 내의 System Call 코드, Interrupt Service Routine을 실행한다.
    - 기존에 실행하던 User Application으로 복귀하여 계속 실행한다.
    
    ![syscall.jpeg](24_07_17_daily_certification%206526efd7a4c843f2a425d0de6332485e/syscall.jpeg)
    

## Process

- 실행 중인 Program
- Program은 Secondary Storage에 존재하는 실행 가능한 File 형태의 데이터
- Program을 Memory에 load하여 실행하면 Process가 된다.

## PCB (Process Control Block)

- 모든 Process가 CPU를 동시에 사용할 수는 없다.
- Process는 정해진 시간만큼 돌아가면서 CPU를 사용해서 실행되어야 한다.
- 정해진 시간만큼 CPU를 사용하면 timer interrput가 발생해 CPU를 다른 Process에 양보한다.
- 이를 위해 Process의 정보를 저장하는 자료구조를 PCB라고 한다.

### PCB에 담긴 정보

- **Process ID (PID) :** Process 식별을 위한 고유한 번호
- **Register Value** : PC, SP등 Process 실행에 필요한 레지스터 값을 백업해둔다.
- **Process State :** CPU 사용을 기다리는 중인지, I/O Device 사용을 기다리는 중인지 등등
- **CPU Scheduling Info** : CPU Scheduling을 위한 정보
- **Memory Info** : Process가 저장된 주소 정보, Base Register, Limit Register, Page Table
- **File Info** : Process가 사용한 파일 정보
- **I/O Device Info** : Process가 사용한 I/O Device 정보

# Problem Solving (Algorithm & SQL)

### BOJ 19951 태상이의 훈련소 생활

[](https://www.acmicpc.net/problem/19951)

```kotlin
import java.util.*
import java.io.*

class BOJ_19951 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens : StringTokenizer
    
    private var n = 0
    private var m = 0
    
    private lateinit var arr : IntArray
    private lateinit var add : IntArray
    
    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()
        
        arr = IntArray(n + 1)
        add = IntArray(n + 1)
        
        tokens = StringTokenizer(br.readLine())
        for(i in 1 .. n)
            arr[i] = tokens.nextToken().toInt()
        
        for(q in 0 ..< m) {
            tokens = StringTokenizer(br.readLine())
            val a = tokens.nextToken().toInt()
            val b = tokens.nextToken().toInt()
            val k = tokens.nextToken().toInt()
            
            add[a] += k
            if(b < n)
               add[b + 1] -= k
        }
        
        for(i in 1 .. n) {
            add[i] = add[i - 1] + add[i]
            sb.append("${arr[i] + add[i]} ")
        }
        
        println(sb)
    }
}

fun main() {
    BOJ_19951().solve()
}
```