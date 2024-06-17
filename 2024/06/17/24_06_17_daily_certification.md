# 24_06_17_daily_certification

# Database

# Relational Database의 단점

## 1. 경직된 Schema

쇼핑몰에서 상품의 주문 정보를 아래와 같은 Order 테이블로 관리한다고 생각해보자.

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled.png)

만약 서비스를 확장하여 로켓 배송 기능을 추가하고 싶다. 기존 Order 테이블로는 각 주문이 로켓 배송인지 확인이 불가하므로 rocket_delivery 컬럼을 추가해야 한다.

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%201.png)

**새로운 컬럼을 추가할 때는 반드시 기존 schema가 변경된다.**

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%202.png)

만약 주문에 이벤트를 추가하고 싶으면 event_id 컬럼을 또 추가해야 한다. 또 schema가 변경된다.

주문이 적어서 Order 테이블의 데이터가 적을 경우에는 별 문제가 안될 수 있지만, 데이터가 많을 경우 새로운 컬럼을 추가할 때 위험 부담이 커진다. (Heavy-write로 인한 DB Server 부담, BE Server 부담)

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%203.png)

Relational Database는 schema를 미리 정의하고 정의한 schema에 맞춰서 데이터를 저장하기 때문에, 테이블에 컬럼을 추가하는 등의 확장이 필요할 경우, 유연한 확장성이 부족하다.

## 2. 과도한 JOIN으로 인한 성능 하락

RDB는 중복으로 저장되는 데이터를 제거를 위한 정규화가 필수적으로 진행된다. (1NF, 2NF, 3NF, BCNF)

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%204.png)

하나의 테이블을 여러 테이블로 분리하면서 데이터의 중복 저장은 사라진다.

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%205.png)

하지만 원래 전체 데이터를 조회하기 위해서는 여러 테이블의 JOIN이 필요하다.

JOIN을 하려면 DB Server에서 CPU를 많이 사용하게 된다.

또한 응답 시간이 늘어나서 복잡한 JOIN은 read 성능이 낮아진다.

## 3. Scale-out에 적합하지 않음

### Scale-up vs Scale-out

- Scale-up : 한 대의 컴퓨터의의 성능을 높여서(CPU, Memory, …) 서버의 성능을 높임
- Scale-out : 여러 대의 서버를 두어 하나의 서버에 부하가 심해지는 것을 방지

RDB는 기본적으로 한 대의 컴퓨터에 저장된다. 한 대의 컴퓨터에 하나의 Database가 존재하고 여러 테이블이 존재한다.

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%206.png)

한 대의 DB Server에 많은 read/write 요청이 몰리게 되면 DB Server의 부하가 심해진다.

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%207.png)

RDB는 scale-up을 통해 데이터베이스 성능을 향상시킨다.

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%208.png)

RDB는 또한 replication을 read-only로 두어 select 쿼리 부하를 분산시킨다.

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%209.png)

하지만 write 트래픽이 대부분이라면 primary DB Server에 부하가 심해지는 것은 어쩔 수 없다.

그 외에도 Sharding, Multi-master 방법이 존재한다.

Replication, Sharding, Multi-master와 같은 방법은 Scale-out에 속한다.

**하지만 RDB는 일반적으로 Scale-out에 적합하진 않다.**

- Replication을 Secondary Server로 둘 경우 Primary Server 전체를 복제해야 한다.
- Write 트래픽이 많아서 Sharding할 경우, 데이터를 옮겨야 한다. 서비스가 운영중일 경우 데이터를 옮기는 것은 쉽지 않다.

## 4. Transaction ACID

RDB에는 Transaction ACID 자체는 장점도 있지만 이를 보장하기 위한 trade-off로 단점도 존재한다.

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%2010.png)

대표적으로 Isolation Level을 높일 수록 DB Server의 Performance가 저하된다.

# NoSQL

## 1. NoSQL 등장 배경

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%2011.png)

2000년대 이후 인터넷 보급률이 매우 높아지고 SNS 사용자가 폭발적으로 늘어나면서 기존 RDBMS만으로는 커버할 수 없는 트래픽이 발생한다. **더 높은 처리량/더 짧은 응답 시간/비정형 데이터의 처리가** 요구된다.

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%2012.png)

그래서 등장한 것이 NoSQL이다.

## 2. NoSQL

### Not-Only SQL

RDB가 커버하지 못하는 단점들도 커버하는 DBMS이다.

### 종류

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%2013.png)

종류가 매우 많고 각각의 특색이 존재하며, SQL처럼 표준화가 잘 되어 있지는 않다.

따라서 사용하고자 하는 NoSQL의 특징을 자세히 확인해서 사용해야 한다.

그럼에도 RDB와 비교했을 때 NoSQL의 공통적인 특징이 있다.

## 3. NoSQL의 특징

mongoDB를 예시로 알아보자.

### 3-1. Flexible Schema

RDB에서는 Student 정보를 저장하기 위해 아래와 같은 테이블 schema를 정의해야 했다.

```sql
create table student (
	id INT PRIMARY KEY,
	name VARCHAR(20)
);
```

미리 schema에 정의된 컬럼 외 데이터를 저장하려면 schema를 수정해야 한다.

![1.png](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/1.png)

mongoDB에서는 유연하게 collection schema를 생성할 수 있다.

![2.png](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/2.png)

![3.png](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/3.png)

데이터의 저장도 원하는 데이터를 Key : Value 형태로 저장 가능하다. primitive type만 아니라 JSON, Array 값도 저장 가능하다.

![4.png](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/4.png)

데이터 조회의 경우 찾으려는 속성의 값을 조건으로 찾을 수 있다. 이렇게 찾아진 데이터를 Document라고 한다.

기타 특징은 mongoDB 공식 문서를 찾아보자.

**RDB에서는 schema를 DB Server에서 관리해줬다면 NoSQL에서는 application level에서 schema를 관리해야 한다. (개발자의 부담)**

### 3-2. 중복 허용 (JOIN 회피)

RDB에서는 테이블을 분리해서 데이터의 중복 저장을 최소화한다.

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%2014.png)

NoSQL에서는 중복을 허용하여 JOIN을 회피한다. JOIN을 회피해서 검색 성능을 높인다.

**하지만 application level에서 중복된 데이터들이 일관성 있게 최신 데이터를 유지할 수 있도록 관리해야 한다.**

### 3-3. Scale-out에 최적화되어 있다.

NoSQL DB Servers는 RDBMS Server보다 증설이 쉽다.

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%2015.png)

서버 여러 대로 하나의 클러스터를 구성해서 사용한다.

각 서버 별로 데이터를 나눠서 중복을 허용하여 저장한다. JOIN이 필요 없으므로 collections을 분산해서 저장하면 된다.

RDB의 경우 서버 별로 테이블을 나누어 저장하면 JOIN을 위한 테이블이 여러 서버에 분산되어 Network Traffic까지 발생하여 성능이 더 떨어진다.

### 3-4. High-Throughput

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%2016.png)

**ACID의 일부를 포기하고 High-Throughput, Low-Latency를 추구한다.**

금융 시스템처럼 Consistency가 중요한 환경에서는 매우 조심해서 사용해야 한다.

ACID를 얼마나 보장하고 포기하는지는 NoSQL마다 다르다.

## 4. Redis

### 4-1. Redis의 특징

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%2017.png)

Redis에 데이터 저장하고 조회하기

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%2018.png)

- 메모리에 Key-Value 형태로 데이터를 저장하는 NoSQL DB (in-memory DB)
- 데이터베이스로도 사용되고, 캐시로도 사용된다.
- 여러 데이터 타입을 저장할 수 있다.
- Hash 기반 sharding된 cluster를 구성할 수 있다.
- High Avaliability(고가용성) : Replication, Automatic Failover
- 메모리의 응답성이 SSD보다 빠르므로 cache로 사용된다.

### 4-2. Redis를 cache로 사용하기

**YouTube 예시**

**RDB만 사용하는 경우**

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%2019.png)

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%2020.png)

요청이 많아질 경우, 성능이 떨어지고, 응답이 느려진다.

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%2021.png)

Replication, Secondary Server를 추가할 수도 있지만 Redis Cache를 사용할 수 있다.

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%2022.png)

조회하려는 데이터가 Redis 캐시에 존재하는지 우선 확인한다.

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%2023.png)

조회하려는 데이터가 Redis 캐시에 존재하지 않을 경우 RDB에서 조회한다.

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%2024.png)

RDB에서 가져온 정보를 BE Server에서 Redis 캐시에 저장한다.

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%2025.png)

Redis에는 Key = Primary Key, Value = 영상 관련 데이터 를 저장한다. 동시에 timeout을 같이 저장하여 timeout 동안에만 Redis 캐시에 존재한다.

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%2026.png)

BE Server에서 가공한 정보를 FE에 전달한다.

![Untitled](24_06_17_daily_certification%200dab231eecb84747827592e29b1c92ba/Untitled%2027.png)

같은 요청이 다시 오면 RDB에서 찾을 필요 없이 Redis 캐시에 저장된 정보를 빠르게 가져올 수 있다.

Redis는 Memory Cache이므로 SSD, HDD를 사용하는 DB에 비해 매우 빠르다.

[Redis - The Real-time Data Platform](https://redis.io/)

[NoSQL Databases List by Hosting Data - Updated 2024](https://hostingdata.co.uk/nosql-database/)

# Problem Solving (Algorithm & SQL)

### BOJ 8972 미친 아두이노

[](https://www.acmicpc.net/problem/8972)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.Stack;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = -1;	//	아무것도 없는 칸
	private static final int MINE =  0;	//	내 아두이노 번호
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer tokens;
	
	private static List<Integer>[][] board;	//	보드
	
	private static int R;	//	보드 세로 크기
	private static int C;	//	보드 가로 크기
	
	private static char[] cmdList;
	
	private static Arduino mine;	//	내 아두이노
	
	private static int num = 1;		//	미친 아두이노 번호
	private static final List<Arduino> arduinos = new ArrayList<>();	//	미친 아두이노 목록
	
	public static void main(String[] args) throws IOException {
		tokens = new StringTokenizer(br.readLine());
		R = Integer.parseInt(tokens.nextToken());
		C = Integer.parseInt(tokens.nextToken());
		
		board = new Stack[R][C];
		for(int r = 0; r < R; r++) {
			for(int c = 0; c < C; c++) {
				board[r][c] = new Stack<>();
				board[r][c].add(NONE);
			}
		}
		
		for(int r = 0; r < R; r++) {
			String input = br.readLine();
			for(int c = 0; c < C; c++) {
				char ch = input.charAt(c);
				
				if(ch == 'I') {
					mine = new Arduino(MINE, r, c);
					board[r][c].add(MINE);
				}
				else if(ch == 'R') {
					Arduino arduino = new Arduino(num, r, c);
					arduinos.add(arduino);
					board[r][c].add(num++);
				}
			}
		}
		
		cmdList = br.readLine().toCharArray();
		
		for(int step = 1; step <= cmdList.length; step++) {
			if(!(step12(cmdList[step - 1]) && step34())) {
				System.out.println("kraj " + step);
				return;
			}
			step5();
		}
		
		printBoard();
	}	//	main-end
	
	//	step 1 ~ 2
	//	내 아두이노가 주어진 방향으로 이동함
	//	만약 미친 아두이노가 있는 칸으로 가게 되면 false 반환
	//	그 외에는 위치 갱신하고 true 반환
	private static boolean step12(char cmd) {
		int dir = (cmd - '0');	//	이동하려는 방향
		
		int ny = mine.y + dy[dir];
		int nx = mine.x + dx[dir];	//	이동하려는 칸 
		
		if(board[ny][nx].get(board[ny][nx].size() - 1) > MINE)	//	미친 아두이노가 있는 칸일 경우
			return false;
		
		board[ny][nx].add(MINE);
		board[mine.y][mine.x].remove(Integer.valueOf(MINE));
		mine.y = ny;
		mine.x = nx;	//	내 아두이노 위치 갱신 
		
		return true;
	}
	
	//	step 3 ~ 4
	//	미친 아두이노가 내 아두이노와 가까워지는 방향으로 이동함
	//	미친 아두이노 중 하나라도 내 아두이노와 충돌하면 false 반환
	//	그 외에는 true 반환
	private static boolean step34() {
		for(Arduino arduino : arduinos) {
			if(!arduino.move())	//	이 미친 아두이노가 내 아두이노와 충돌할 경우
				return false;
		}
		
		return true;
	}
	
	//	step 5
	//	어느 한 칸에 미친 아두이노가 두 개 이상 있을 경우 해당 칸의 미친 아두이노를 모두 무력화함
	private static void step5() {
		for(int r = 0; r < R; r++) {
			for(int c = 0; c < C; c++) {
				int state = board[r][c].get(board[r][c].size() - 1);
				
				if(state == NONE || state == MINE)
					continue;
				
				if(board[r][c].size() >= 3) {	//	NONE을 제외하고 2개 이상 있다는 것은 미친 아두이노가 2개 이상 있다는 것임
					for(int i = board[r][c].size() - 1; i > 0; i--) {
						int num = board[r][c].remove(i);	//	해당 칸에 있는 미친 아두이노 제거
						arduinos.get(num - 1).disabled = true;	//	무력화시키기
					}
				}
			}
		}
	}
	
	private static void printBoard() {
		for(int r = 0; r < R; r++) {
			for(int c = 0; c < C; c++) {
				int state = board[r][c].get(board[r][c].size() - 1);
				
				sb.append(state == NONE ? '.' : (state == MINE ? 'I' : 'R'));
			}
			sb.append("\n");
		}
		
		System.out.print(sb);
	}
	
	private static final int[] dy = {0, 1, 1, 1, 0, 0, 0, -1, -1, -1};
	private static final int[] dx = {0, -1, 0, 1, -1, 0, 1, -1, 0, 1};
	
	public static boolean isIn(int y, int x) {
		return (0 <= y && y < R) && (0 <= x && x < C);
	}
	
	private static class Arduino {
		final int num;	//	아두이노 번호 (내껀 0번, 나머지는 1번 부터)
		int y;
		int x;		//	아두이노 위치
		boolean disabled = false;	//	두 개 이상의 미친 아두이노가 같은 칸에 있으면 같은 칸에 있는 모든 아두이노가 무력화됨
		
		public Arduino(int num, int y, int x) {
			this.num = num;
			this.y = y;
			this.x = x;
		}
		
		//	8방향 중 내 아두이노와 가까워지는 방향으로 이동
		//	이미 무력화된 아두이노면 true 반환
		//	가장 가까워지는 방향으로 이동했는데 내 아두이노와 만나면 false 반환
		//	그 외에는 true 반환
		public boolean move() {
			if(this.disabled)
				return true;
			
			int minDist = Integer.MAX_VALUE;	//	내 아두이노와 가장 가까운 거리
			int minDir = -1;					//	그 때 이동방향
			int ny = 0;
			int nx = 0;
	
			for(int d = 1; d <= 9; d++) {
				ny = this.y + dy[d];
				nx = this.x + dx[d];
				
				if(d == 5 || !isIn(ny, nx))	//	제자리에 있거나 범위를 벗어날 경우
					continue;
				
				int dist = manhattanDist(ny, nx);
				
				if(dist < minDist) {
					minDist = dist;
					minDir = d;
				}
			}

			ny = this.y + dy[minDir];
			nx = this.x + dx[minDir];
			
			if(board[ny][nx].get(board[ny][nx].size() - 1) == MINE)	//	내 아두이노와 미친 아두이노가 부딪힐 경우
				return false;
			
			board[this.y][this.x].remove(Integer.valueOf(this.num));	//	현재 위치에서 미친 아두이노 제거
			board[ny][nx].add(this.num);	//	이동할 위치로 미친 아두이노 이동
			
			this.y = ny;
			this.x = nx;	//	미친 아두이노 위치 갱신
			
			return true;
		}
		
		private int manhattanDist(int ny, int nx) {
			return Math.abs(ny - mine.y) + Math.abs(nx - mine.x);
		}
	}
}	//	Main-class-end
```