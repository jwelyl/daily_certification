# 24_06_10_daily_certification

# Database

# DBCP

## DBCP(Database Connection Pool)

## 문제 제기

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled.png)

Front-End에서 API 요청이 오면 Back-End Server에서 API 요청을 받아 처리한다. 처리 중 Database에 접근해야 할 경우 DB Server에 Query 요청을 보내고 Query에 대한 응답을 받아서 API 요청을 마저 처리 후 API 요청에 대한 응답을 FE에 보낸다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%201.png)

실제 Back-End Server와 DB. Server는 다른 컴퓨터에 존재하는 경우가 대다수이기 때문에 둘 사이의 통신은 TCP 기반 네트워크 통신이다.

### **TCP 기반 통신**

**장점**

- 높은 송수신 신뢰성
- 데이터 송수신 시작 전 Open Connection (3-Way Handshake)
- 데이터 송수신 종료 후 Close Connection (4-Way Handshake)

**단점**

- Connection Open/Close의 시간적인 오버헤드가 작지 않음
- BE 관점에서 DB Server로 요청을 보내고 받을 때마다 매번 Connection 열고 닫으면 오버헤드가 매우 큼
- 서비스 성능이 떨어질 수 있음

## DBCP(Database Connection Pool)

TCP 기반 Connection을 DB에 접근할 때마다 open/close하는 단점을 보완

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%202.png)

미리 Back-End Server와 DB Server의 Connection을 여러 개 open해둔다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%203.png)

미리 연결된 Connection들을 Pool에 관리한다.

이렇게 미리 연결해둔 Connection을 하나의 Pool로 만들어서 관리한다.

**이 Pool을 Database Connection Pool (DBCP)라고 한다.**

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%204.png)

FE에서 API 요청을 받아 처리 중 DB Server에 접근해야 할 일이 발생한다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%205.png)

새로 TCP 3-Way Handshake로 Connection을 여는 것이 아니라, 기존에 open해둔 connection 중, 사용되지 않는 connection을 DBCP에서 얻어온다. **(Get Connection)**

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%206.png)

얻어온 connection을 통해 DB Server와 연결하여 쿼리 요청을 보내고 응답을 받는다. 응답을 받은 후 실제로 4-Way Handshake를 통해 connection을 종료하는 것이 아니라 connection을 DBCP에 반납한다. **(Close Connection)**

DB Server의 쿼리 응답으로 API의 요청을 처리하고 FE에 결과를 보내준다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%207.png)

API 요청마다 TCP Connection을 open/close할 필요가 없어서 시간이 절약된다.

## DB Server, DBCP 설정(Configuration) 방법

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%208.png)

### MySQL, HikariCP (Springboot 2.0 이상)

DB Connection은 BE Server와 DB Server 사이의 연결이므로, BE Server와 DB Server 각각에서 설정(Configuration) 방법을 알아야 한다.

## DB Server Configuration

### MySQL Configuration

**max_connections, wait_timeout**

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%209.png)

**max_connections**

client(Back-End Server)와 맫을 수 있는 최대 connection 수

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2010.png)

최대 4개의 connection이 가능하고, 이미 DBCP에 4개의 connection이 존재한다고 가정할 때,

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2011.png)

Back-End Server에 트래픽이 몰릴 경우 Back-End Server의 과부하가 걸리게 된다.(CPU, Memory 등등)

이를 방지하기 위해 WAS를 한 대 더 투입해 트래픽을 분산시키려 한다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2012.png)

하지만 새로운 Back-End Server를 실행시키기 위해서는 DBCP가 DB Server와 Connection을 미리 맺어야 하는데 DB Server의 max_connection만큼 이미 기존 Back-End Server와 connection을 맺고 있기 때문에 맺을 수 없다.

**따라서 DB Server의 max_connection이 적절히 설정되어야 한다.**

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2013.png)

**wait_timeout**

connection이 inactive할 때 다시 요청이 오기까지 얼마의 시간을 기다린 뒤에 close할 지 결정

connection을 하염없이 open한 상태로 유지하는 것도 resource 낭비로 DB Server에 악영향을 준다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2014.png)

정상적인 경우에는 Connection이 close되면 BE Server에서 connection을 끊고 DB Server에서도 connection을 끊어 리소스를 반환하면 된다.

하지만 비정상적으로 connection을 종료하거나, connection이 사용은 안되지만 반환되지 않거나, 네트워크가 단절될 경우에도 DB Server 입장에서는 연결이 정상적이라고 생각하여 BE Server로부터 요청을 기다리며 resource를 낭비하게 된다. 이는 DB Server에 악영향을 준다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2015.png)

이를 방지하기 위해 DB Server에서 wait_timeout을 설정하고, wait_timeout 동안 기다린 후에도 요청이 없으면 connection을 close한다.

만약 wait_timeout이 끝나기 전에 요청이 도착하면 요청에 대한 응답을 보내준 후 다시 wait_timeout 동안 기다린다.

## DBCP Configuration

### HikariCP Configuration

**minimumIdle vs maximumPoolSize vs maxLifetime vs** 

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2016.png)

**minimumIdle**

pool에서 아무 일을 하지 않더라도 유지하고 있는 최소한의 Idle Connection의 수

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2017.png)

**maximumPoolSize**

pool이 가질 수 있는 최대 connection 수

idle(inactive) connection 수 + active(in-use) connection 수

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2018.png)

**minimumIdle**

pool에서 아무 일을 하지 않더라도 유지하고 있는 최소한의 Idle Connection의 수

**DBCP의 Idle Connection 수가 minimumIdle보다 적고, 전체 Connection 수가 maximumPoolSize보다 적다면 신속하게 추가로 connection을 만든다.**

ex) minimumIdle = 2, maximumPoolSize = 4

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2019.png)

초기 상태는 minimumIdle 개수만큼 Idle Connection이 존재

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2020.png)

BE Server에 하나의 요청이 들어와서 idle connection 1개가 사용됨(active connection)

Idle connection 수는 1개로 minimumIdle보다 적고, 전체 connection 수도 2개로 maximumConnection 수보다 적음

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2021.png)

따라서 새로운 connection을 하나 생성한다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2022.png)

트래픽이 계속 와서 idle connection이 minumumIdle보다 적지만, 전체 connection이 maximumPoolSize만큼 존재하면 더 이상 idle connection은 생성할 수 없다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2023.png)

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2024.png)

트래픽이 더 이상 존재하지 않으면 모든 connection이 idle connection이 되고, minimumIdle개만 남기고 모두 close한다.

**HikariCP의 권장 사항은 minumumIdle을 maximumPoolSize로 설정하는 것이다.**

즉 항상 maximumPoolSize개의 connection을 유지한다. 트래픽이 몰려올 때마다 connection을 새로 추가할 필요가 없으므로 connection 생성 시간을 단축할 수 있다.

**maxLifetime**

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2025.png)

DBCP의 idle connection은 maxLifetime을 넘기면 pool에서 제거된다.

active connection의 경우 pool로 반환된 후 pool에서 제거된다.

idle connection이 4개인 상태에서 1개가 maxLifetime을 넘기면 pool에서 제거되면 빠르게 새로운 idle connection이 추가된다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2026.png)

**pool로 반환되지 않으면 maxLifetime을 지나도 pool에서 제거되지 않는다.**

active connection이 다 사용된 후 반환되지 않으면 pool로 반환되지 않으므로 제거되지 않는다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2027.png)

maxLifetime = 60인데, active connection이 반환되지 않으면 DB Server에서는 해당 connection을 active하다고 인식한다.

DB Server는 wait_timeout 동안 기다리고, 그 동안 해당 connection에서 어떤 요청도 오지 않으면 connection을 close한다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2028.png)

만약 그 이후 DB Server에서 해당 connection으로 요청을 보내려 해도 이미 close되서 Exception이 발생한다. **따라서 사용이 끝난 connection은 즉시 pool로 반환해줘야 한다.**

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2029.png)

maxLifetime은 DB Connection time limit(wait_timeout)보다 몇 초(2 ~ 3초) 정도 짧게 해야 한다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2030.png)

**connectionTimeout**

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2031.png)

BE Server로 요청이 와서 DB Server에 접근할 일이 생겼을 때 DBCP로부터 Connection을 받기 위해 대기해야 하는 시간이다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2032.png)

connectionTimeout이 30초이면, 트래픽이 엄청 몰려오고 모든 connection이 active일 때 DBCP에서 connection이 idle해질 때 까지 최대 30초를 기다린다. 

connectionTimeout이 30초가 지나도록 받지 못한다면 Exception을 발생시킨다. 

connectionTimeout을 잘 설정해야 한다.

보통 일반적인 사용자는 요청을 보내고 30초씩 기다리지 않는다. 많아야 10초 정도 기다린다.

사용자는 10초 정도 기다리다 응답이 없으면 나가는데(Client로부터 연결이 끊긴다.) 29초 즘에 DBCP로부터 connection을 받아서 DB 요청과 응답을 받아서 API 응답을 client에게 주려해도, 이미 client와 연결이 끊겼으므로 응답을 줄 수 없다. 이 경우, connectionTImeout 30초는 너무 길다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2033.png)

**DB Server Parameter, DBCP Parameter를 적절히 잘 설정해야 한다.**

## cf. 적절한 Connection 수 정하기

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2034.png)

Primary DB Server는 Read/Write 둘다 처리

Secondary DB Server(Replication)는 Read만 처리

일단 Primary DB Server만 고려

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2035.png)

**부하 테스트**

- 요청을 조금씩 늘려가면서 BE Server가 어떻게 동작하는 지 테스트한다.
- request per second
- average response time
- nGrinder (NAVER)

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2036.png)

트래픽이 많아질 수록 request per second는 더 이상 증가하지 못하고, average response time은 크게 증가한다. 즉, 성능이 떨어진다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2037.png)

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2038.png)

BE Server의 CPU, Memory 리소스 사용량을 확인해보고 일정 수준 이상이면(60% 이상), 현재 BE Server만으로는 트래픽을 처리를 못하므로 추가해줘야 한다. BE Server 추가를 통해 트래픽을 분산시킬 수 있다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2039.png)

만약 Server의 CPU, Memory 리소스 사용량이 일정 수준 이상이면 여러 방법이 있다.

- Secondary Server 추가
    
    select 쿼리를 분산시킬 수 있다.
    
- Cache Layer
    
    DB Server가 직접 받는 부하를 줄여준다
    
- Sharding

만약 BE Server, DB Server 모두 적정량의 리소스를 사용하는데도 성능이 일정 트래픽 이상에서 급격히 나빠진다면 

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2040.png)

**thread per request 모델**

request마다 thread를 할당하는 모델이라면, thread pool의 active thread 수를 확인하고 필요 시 늘려준다.

만약 thread pool의 여우 thread가 많다면 thread 문제는 아니다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2041.png)

이 때 DBCP의 active connection의 수를 확인하고 BE Server의 maximumPoolSize를 올려준다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2042.png)

maximumPoolSIze 합이 DB Server의 max_connections 수와 같을 때 까지 늘려본다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2043.png)

그 이후에는 DB Server의 max_connections를 증가시키고 그에 맞게 maximumPoolSize를 증가시켜본다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2044.png)

이렇게 각 parameter들을 조정하면서 원하는 트래픽을 잘 처리하는 값을 찾는다.

![Untitled](24_06_10_daily_certification%209694524834cd43f482a0878914d7c95d/Untitled%2045.png)

**DB Server의 max_connections 수를 먼저 정하고, 그에 맞춰 BE Server 수(예비 Server)를 고려해 maximumPoolSize를 정하면 된다.**

**참고**

[Commons DBCP 이해하기](https://d2.naver.com/helloworld/5102792)

# Problem Solving (Algorithm & SQL)

### BOJ 3987 보이저 1호

[](https://www.acmicpc.net/problem/3987)

```kotlin
import java.util.*
import java.io.*

const val U = 0
const val R = 1
const val D = 2
const val L = 3

const val INF = Int.MAX_VALUE
const val NONE = -1

class Main {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var n = 0
    private var m = 0    //    맵 크기

    //    visited[dir][row][col] : (row, col)을 dir 상태로 방문했으면 true
    private lateinit var visited : Array<Array<BooleanArray>>
    private lateinit var map : Array<CharArray>

    private var pr = 0
    private var pc = 0    //    보이저 1호 위치

    private val dirChar = charArrayOf('U', 'R', 'D', 'L')
    private val dy = intArrayOf(-1, 0, 1, 0)
    private val dx = intArrayOf(0, 1, 0, -1)

    private var maxDir = 'U'       //    가장 멀리 전파 보낼 수 있는 방향
    private var maxTime = NONE     //    그 때 전파 시간

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        visited = Array(4) { Array(n) { BooleanArray(m) } }
        map = Array(n) { CharArray(m) }

        for(i in 0 ..< n) {
            val input = br.readLine()
            for(j in 0 ..< m)
                map[i][j] = input[j]
        }

        tokens = StringTokenizer(br.readLine())
        pr = tokens.nextToken().toInt() - 1
        pc = tokens.nextToken().toInt() - 1

        for(dir in 0 ..< 4) {    //    4방향 다 전파 보내보기
            val time = send(dir)    //    dir 방향으로 전파 보냈을 때 시간

            if(time > maxTime) {    //    전파가 더 오래 진행할 수 있을 경우
                maxDir = dirChar[dir]
                maxTime = time
            }
        }

        println(maxDir)
        println(if(maxTime == INF) "Voyager" else maxTime)
    }

    //    dir 방향으로 전파를 보냈을 때 블랙홀에 도달하거나 항성계를 벗어날 때까지의 시간
    //    영원히 블랙홀에 도달하거나 항성계를 벗어날 수 없으면 INF
    private fun send(dir : Int) : Int {
        reset()

        var cy = pr
        var cx = pc        //    전파 현재 위치
        var ctime = 0      //    현재 시각
        var cdir = dir     //    전파 현재 방향

        visited[cdir][cy][cx] = true

        while(true) {
            val ny = cy + dy[cdir]
            val nx = cx + dx[cdir]    //    다음 칸
            ctime++    //    다음 칸으로 이동했을 때 시간

            if(!isIn(ny, nx))    //  항성계를 벗어났거나, 블랙홀에 빠질 경우
                break

            if(visited[cdir][ny][nx])    //    이번 칸에 이번 방향으로 온 적이 있을 경우
                return INF

            visited[cdir][ny][nx] = true    //    방문 상태 변경
            cy = ny
            cx = nx

            if(map[ny][nx] != '.')    //    행성일 경우
                cdir = nextDir(cdir, map[ny][nx])    //    행성에 의해 방향 변화
        }

        return ctime
    }

    private fun nextDir(curDir : Int, planet : Char) : Int {
        if(planet == '\\') {
            return when(curDir) {
                U -> L
                L -> U
                R -> D
                else -> R
            }
        }
        else {
            return when(curDir) {
                U -> R
                R -> U
                L -> D
                else -> L
            }
        }
    }

    //    방문배열 초기화
    private fun reset() {
        for(d in 0 ..< 4) {
            for(r in 0 ..< n) {
                for(c in 0 ..< m)
                    visited[d][r][c] = false
            }
        }
    }

    //    현재 위치가 항성계 내부이면서 블랙홀이 아니면 true
    private fun isIn(y : Int, x : Int ) : Boolean {
        return (y in 0 ..< n && x in 0 ..< m) && (map[y][x] != 'C')
    }
}

fun main() {
    Main().solve()
}
```