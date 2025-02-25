# 25_02_24_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2001 보석 줍기

[2001번: 보석 줍기](http://boj.ma/2001/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = 0;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;	//	섬의 개수
	private static int M;	//	다리 개수
	private static int K;	//	보석 개수
	
	private static int[] gems;	//	gems[v] : v번째 섬에 있는 보석 번호 (NONE이면 보석 없음, 1 ~ K)

	private static List<int[]>[] graph;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		gems = new int[N + 1];
		for(int k = 1; k <= K; k++)
			gems[Integer.parseInt(br.readLine())] = k;	//	k번째 보석이 있는 섬 위치 입력
		
		graph = new ArrayList[N + 1];
		for(int v = 0; v <= N; v++)
			graph[v] = new ArrayList<>();
		
		for(int e = 0; e < M; e++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			int limit = Integer.parseInt(st.nextToken());
			
			graph[v1].add(new int[] {v2, limit});
			graph[v2].add(new int[] {v1, limit});	//	v1, v2 사이를 오가기 위해서는 보석을 limit개 이하로 가지고 있어야 함
		}
		
		bfs();
	}	//	main-end
	
	private static void bfs() {
		Queue<int[]> queue = new LinkedList<>();
		//	K개 보석을 가지는 경우의 수 = 0(보석 한 개도 없는 경우) ~ 2^K - 1(K개 보석을 모두 가지는 경우)
		boolean[][] visited = new boolean[N + 1][1 << K];
		int answer = 0;						//	1에 최대로 가져올 수 있는 보석 개수
		
		visited[1][0] = true;
		queue.offer(new int[] {1, 0});
		if(gems[1] != NONE) {	//	출발지인 1번 섬에 보석이 있을 경우
			int status = getGem(0, gems[1]);
			visited[1][status] = true;
			queue.offer(new int[] {1, status});
		}
		
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cv = cur[0];
			int cstatus = cur[1];				//	현재 보석 상태
			int ccnt = countGems(cstatus);		//	현재 보석 개수
			
			for(int[] edge : graph[cv]) {
				int nv = edge[0];
				int limit = edge[1];	//	cv에서 nv로 넘어가기 위한 무게 제한
				
				if(ccnt <= limit) {		//	cv에서 nv로 넘어갈 수 있는 경우
					if(gems[nv] == NONE) {	//	nv에는 보석이 없는 경우
						if(!visited[nv][cstatus]) {	//	nv에 현재 상태로 방문한 적이 없을 경우
							visited[nv][cstatus] = true;
							queue.offer(new int[] {nv, cstatus});
						}
					}
					else {	//	nv에 보석이 존재할 경우
						int gem = gems[nv];
						
						if(!visited[nv][cstatus]) {	//	nv에 현재 상태로 방문한 적이 없을 경우
							visited[nv][cstatus] = true;
							queue.offer(new int[] {nv, cstatus});
							
							if(!containsGem(cstatus, gem)) {	//	nv에서 보석을 얻은 적은 없을 경우
								int nstatus = getGem(cstatus, gem);
								visited[nv][nstatus] = true;
								queue.offer(new int[] {nv, nstatus});
							}
						}
					}
				}
			}
		}
		
		for(int status = (1 << K) - 1; status >= 0; status--) {
			if(visited[1][status]) {
				int cnt = countGems(status);
				
				answer = Math.max(answer, cnt);
			}
		}
		
		System.out.println(answer);
	}
	
	//	현재 상태 status에서 gem을 가지고 있으면 true
	private static boolean containsGem(int status, int gem) {
		return (status & (1 << (gem - 1))) == 1;
	}
	
	//	현재 상태 status에서 gem 보석을 추가로 얻었을때 상태
	private static int getGem(int status, int gem) {
		return status | (1 << (gem - 1));
	}
	
	//	status를 이진수로 변경했을 때 bit 1의 개수
	//	보석 주운 상태를 status라 할때 주운 보석 개수
	private static int countGems(int status) {
		int res = 0;
		
		while(status > 0) {
			if((status & 1) == 1)
				res++;
			
			status >>= 1;
		}
		
		return res;
	}
}	//	Main-class-end
```

[FREE AI-Enhanced Online Oracle Compiler - For learning & practice](https://sqlfiddle.com/oracle/online-compiler?id=1971efde-c22b-428b-8114-2f7fc53d6e76)

53번 실습 예제

```sql
CREATE TABLE EMP (
    EMPNO    NUMBER(4) PRIMARY KEY, -- 사원번호 (기본키)
    ENAME    VARCHAR2(50),           -- 사원 이름
    JOB      VARCHAR2(50),           -- 직책
    MGR      NUMBER(4),              -- 매니저 사원번호 (NULL 가능)
    HIREDATE DATE,                   -- 입사일
    SAL      NUMBER(10,2),           -- 급여
    COMM     NUMBER(10,2),           -- 커미션 (NULL 가능)
    DEPTNO   NUMBER(2)               -- 부서번호
);

INSERT INTO EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO) VALUES 
(7369, 'SMITH', 'CLERK', 7902, TO_DATE('1980-12-17', 'YYYY-MM-DD'), 800.00, NULL, 20);

INSERT INTO EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO) VALUES 
(7499, 'ALLEN', 'SALESMAN', 7698, TO_DATE('1981-02-20', 'YYYY-MM-DD'), 1600.00, 300.00, 30);

INSERT INTO EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO) VALUES 
(7521, 'WARD', 'SALESMAN', 7698, TO_DATE('1981-02-22', 'YYYY-MM-DD'), 1250.00, 500.00, 30);

INSERT INTO EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO) VALUES 
(7566, 'JONES', 'MANAGER', 7839, TO_DATE('1981-04-02', 'YYYY-MM-DD'), 2975.00, NULL, 20);

INSERT INTO EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO) VALUES 
(7654, 'MARTIN', 'SALESMAN', 7698, TO_DATE('1981-09-28', 'YYYY-MM-DD'), 1250.00, 1400.00, 30);

INSERT INTO EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO) VALUES 
(7698, 'BLAKE', 'MANAGER', 7839, TO_DATE('1981-05-01', 'YYYY-MM-DD'), 2850.00, NULL, 30);

INSERT INTO EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO) VALUES 
(7782, 'CLARK', 'MANAGER', 7839, TO_DATE('1981-06-09', 'YYYY-MM-DD'), 2450.00, NULL, 10);

INSERT INTO EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO) VALUES 
(7788, 'SCOTT', 'ANALYST', 7566, TO_DATE('1987-07-13', 'YYYY-MM-DD'), 3000.00, NULL, 20);

INSERT INTO EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO) VALUES 
(7839, 'KING', 'PRESIDENT', NULL, TO_DATE('1981-11-17', 'YYYY-MM-DD'), 5000.00, NULL, 10);

INSERT INTO EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO) VALUES 
(7844, 'TURNER', 'SALESMAN', 7698, TO_DATE('1981-09-08', 'YYYY-MM-DD'), 1500.00, 0.00, 30);

INSERT INTO EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO) VALUES 
(7876, 'ADAMS', 'CLERK', 7788, TO_DATE('1987-07-13', 'YYYY-MM-DD'), 1100.00, NULL, 20);

INSERT INTO EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO) VALUES 
(7900, 'JAMES', 'CLERK', 7698, TO_DATE('1981-12-03', 'YYYY-MM-DD'), 950.00, NULL, 30);

INSERT INTO EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO) VALUES 
(7902, 'FORD', 'ANALYST', 7566, TO_DATE('1981-12-03', 'YYYY-MM-DD'), 3000.00, NULL, 20);

INSERT INTO EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO) VALUES 
(7934, 'MILLER', 'CLERK', 7782, TO_DATE('1982-01-23', 'YYYY-MM-DD'), 1300.00, NULL, 10);

```

54번 실습 예제

```sql
CREATE TABLE SAMPLE_TABLE (
    A VARCHAR2(10) PRIMARY KEY, -- 'A' 컬럼은 기본 키로 설정
    B VARCHAR2(10) NOT NULL,     -- 'B' 컬럼은 NOT NULL 제약조건
    C NUMBER(10) NOT NULL       -- 'C' 컬럼은 숫자형
);

INSERT INTO SAMPLE_TABLE (A, B, C) VALUES ('001', 'A001', 100);
INSERT INTO SAMPLE_TABLE (A, B, C) VALUES ('002', 'A001', 200);
INSERT INTO SAMPLE_TABLE (A, B, C) VALUES ('003', 'A002', 100);
INSERT INTO SAMPLE_TABLE (A, B, C) VALUES ('004', 'A002', 200);
INSERT INTO SAMPLE_TABLE (A, B, C) VALUES ('005', 'A002', 200);
INSERT INTO SAMPLE_TABLE (A, B, C) VALUES ('006', 'A003', 100);
INSERT INTO SAMPLE_TABLE (A, B, C) VALUES ('007', 'A003', 200);
INSERT INTO SAMPLE_TABLE (A, B, C) VALUES ('008', 'A003', 100);
INSERT INTO SAMPLE_TABLE (A, B, C) VALUES ('009', 'A003', 200);
INSERT INTO SAMPLE_TABLE (A, B, C) VALUES ('010', 'A004', 200);

SELECT * FROM SAMPLE_TABLE;
```

58번 실습 예제

```sql
CREATE TABLE TBL (
    ID VARCHAR2(1),
    AMT INT NOT NULL
);

INSERT INTO TBL VALUES('B', 300);
INSERT INTO TBL VALUES('C', 100);
INSERT INTO TBL VALUES('A', 50);
INSERT INTO TBL VALUES('A', 200);

SELECT *
FROM TBL
ORDER BY (CASE WHEN ID = 'A' THEN 1 ELSE 2 END), AMT DESC;
```

60번 실습 예제

```sql
CREATE TABLE TEAM_STATS (
    TEAM_NAME VARCHAR2(10) PRIMARY KEY,  -- 팀명 (기본 키)
    WINS NUMBER(10) NOT NULL,            -- 승리 건수
    LOSSES NUMBER(10) NOT NULL           -- 패배 건수
);

INSERT INTO TEAM_STATS (TEAM_NAME, WINS, LOSSES) VALUES ('A_TEAM', 120, 80);
INSERT INTO TEAM_STATS (TEAM_NAME, WINS, LOSSES) VALUES ('B_TEAM', 20, 180);
INSERT INTO TEAM_STATS (TEAM_NAME, WINS, LOSSES) VALUES ('C_TEAM', 10, 190);
INSERT INTO TEAM_STATS (TEAM_NAME, WINS, LOSSES) VALUES ('D_TEAM', 100, 100);
INSERT INTO TEAM_STATS (TEAM_NAME, WINS, LOSSES) VALUES ('E_TEAM', 110, 90);
INSERT INTO TEAM_STATS (TEAM_NAME, WINS, LOSSES) VALUES ('F_TEAM', 100, 100);
INSERT INTO TEAM_STATS (TEAM_NAME, WINS, LOSSES) VALUES ('G_TEAM', 70, 130);

SELECT * FROM TEAM_STATS;
```

66번 실습 예제

```sql
-- 고객 테이블 생성
CREATE TABLE CUSTOMER (
    CUSTOMER_ID VARCHAR2(10) PRIMARY KEY, -- 고객 ID
    CUSTOMER_NAME VARCHAR2(50) NOT NULL,  -- 고객명
    AGE NUMBER(3) NOT NULL               -- 나이
);

-- 콘텐츠 테이블 생성
CREATE TABLE CONTENT (
    CONTENT_ID VARCHAR2(10) PRIMARY KEY, -- 콘텐츠 ID
    CONTENT_NAME VARCHAR2(100) NOT NULL  -- 콘텐츠명
);

-- 추천 콘텐츠 테이블 생성
CREATE TABLE RECOMMENDED_CONTENT (
    CUSTOMER_ID VARCHAR2(10),             -- 고객 ID (FK)
    CONTENT_ID VARCHAR2(10),              -- 콘텐츠 ID (FK)
    RECOMMENDED_DATE DATE NOT NULL,       -- 추천 대상 일자
    PRIMARY KEY (CUSTOMER_ID, CONTENT_ID),
    FOREIGN KEY (CUSTOMER_ID) REFERENCES CUSTOMER(CUSTOMER_ID),
    FOREIGN KEY (CONTENT_ID) REFERENCES CONTENT(CONTENT_ID)
);

-- 비선호 콘텐츠 테이블 생성
CREATE TABLE DISLIKED_CONTENT (
    CUSTOMER_ID VARCHAR2(10),             -- 고객 ID (FK)
    CONTENT_ID VARCHAR2(10),              -- 콘텐츠 ID (FK)
    REGISTERED_DATE DATE NOT NULL,        -- 등록 일시
    PRIMARY KEY (CUSTOMER_ID, CONTENT_ID),
    FOREIGN KEY (CUSTOMER_ID) REFERENCES CUSTOMER(CUSTOMER_ID),
    FOREIGN KEY (CONTENT_ID) REFERENCES CONTENT(CONTENT_ID)
);

-- 고객 데이터 삽입 (이름을 영어로 변경)
INSERT INTO CUSTOMER (CUSTOMER_ID, CUSTOMER_NAME, AGE) VALUES ('000001', 'Rain', 38);
INSERT INTO CUSTOMER (CUSTOMER_ID, CUSTOMER_NAME, AGE) VALUES ('000002', 'Lee Jung-hwa', 25);
INSERT INTO CUSTOMER (CUSTOMER_ID, CUSTOMER_NAME, AGE) VALUES ('000003', 'Jang Na-ra', 26);
INSERT INTO CUSTOMER (CUSTOMER_ID, CUSTOMER_NAME, AGE) VALUES ('000004', 'Lee Hyo-ri', 35);
INSERT INTO CUSTOMER (CUSTOMER_ID, CUSTOMER_NAME, AGE) VALUES ('000005', 'Hyun Bin', 35);
INSERT INTO CUSTOMER (CUSTOMER_ID, CUSTOMER_NAME, AGE) VALUES ('000006', 'Jung Woo-sung', 40);
INSERT INTO CUSTOMER (CUSTOMER_ID, CUSTOMER_NAME, AGE) VALUES ('000007', 'Song Hye-kyo', 36);
INSERT INTO CUSTOMER (CUSTOMER_ID, CUSTOMER_NAME, AGE) VALUES ('000008', 'Song Joong-ki', 34);
INSERT INTO CUSTOMER (CUSTOMER_ID, CUSTOMER_NAME, AGE) VALUES ('000009', 'Lee Seok-seok', 30);
INSERT INTO CUSTOMER (CUSTOMER_ID, CUSTOMER_NAME, AGE) VALUES ('000010', 'Won Bin', 38);

-- 콘텐츠 데이터 삽입 (콘텐츠명을 영어로 변경)
INSERT INTO CONTENT (CONTENT_ID, CONTENT_NAME) VALUES ('C00001', 'The Glory');
INSERT INTO CONTENT (CONTENT_ID, CONTENT_NAME) VALUES ('C00002', 'Agency');
INSERT INTO CONTENT (CONTENT_ID, CONTENT_NAME) VALUES ('C00003', 'Taxi Driver 2');
INSERT INTO CONTENT (CONTENT_ID, CONTENT_NAME) VALUES ('C00004', 'The Glory');
INSERT INTO CONTENT (CONTENT_ID, CONTENT_NAME) VALUES ('C00005', 'Youth Monthly Talk');
INSERT INTO CONTENT (CONTENT_ID, CONTENT_NAME) VALUES ('C00006', 'One Hit Scandal');
INSERT INTO CONTENT (CONTENT_ID, CONTENT_NAME) VALUES ('C00007', 'Big Bet');

-- 추천 콘텐츠 데이터 삽입
INSERT INTO RECOMMENDED_CONTENT (CUSTOMER_ID, CONTENT_ID, RECOMMENDED_DATE) VALUES ('000001', 'C00007', TO_DATE('2023-02-07', 'YYYY-MM-DD'));
INSERT INTO RECOMMENDED_CONTENT (CUSTOMER_ID, CONTENT_ID, RECOMMENDED_DATE) VALUES ('000010', 'C00004', TO_DATE('2023-02-07', 'YYYY-MM-DD'));
INSERT INTO RECOMMENDED_CONTENT (CUSTOMER_ID, CONTENT_ID, RECOMMENDED_DATE) VALUES ('000001', 'C00003', TO_DATE('2023-02-07', 'YYYY-MM-DD'));
INSERT INTO RECOMMENDED_CONTENT (CUSTOMER_ID, CONTENT_ID, RECOMMENDED_DATE) VALUES ('000004', 'C00005', TO_DATE('2023-02-07', 'YYYY-MM-DD'));
INSERT INTO RECOMMENDED_CONTENT (CUSTOMER_ID, CONTENT_ID, RECOMMENDED_DATE) VALUES ('000005', 'C00007', TO_DATE('2023-02-07', 'YYYY-MM-DD'));
INSERT INTO RECOMMENDED_CONTENT (CUSTOMER_ID, CONTENT_ID, RECOMMENDED_DATE) VALUES ('000005', 'C00005', TO_DATE('2023-02-07', 'YYYY-MM-DD'));
INSERT INTO RECOMMENDED_CONTENT (CUSTOMER_ID, CONTENT_ID, RECOMMENDED_DATE) VALUES ('000002', 'C00002', TO_DATE('2023-02-07', 'YYYY-MM-DD'));
INSERT INTO RECOMMENDED_CONTENT (CUSTOMER_ID, CONTENT_ID, RECOMMENDED_DATE) VALUES ('000001', 'C00004', TO_DATE('2023-02-07', 'YYYY-MM-DD'));

-- 비선호 콘텐츠 데이터 삽입
INSERT INTO DISLIKED_CONTENT (CUSTOMER_ID, CONTENT_ID, REGISTERED_DATE) VALUES ('000001', 'C00004', TO_DATE('2023-01-01', 'YYYY-MM-DD'));
INSERT INTO DISLIKED_CONTENT (CUSTOMER_ID, CONTENT_ID, REGISTERED_DATE) VALUES ('000004', 'C00005', TO_DATE('2023-01-01', 'YYYY-MM-DD'));
INSERT INTO DISLIKED_CONTENT (CUSTOMER_ID, CONTENT_ID, REGISTERED_DATE) VALUES ('000005', 'C00003', TO_DATE('2023-01-01', 'YYYY-MM-DD'));

-- 데이터 확인용 SELECT 문
--SELECT * FROM CUSTOMER;
--SELECT * FROM CONTENT;
--SELECT * FROM RECOMMENDED_CONTENT;
--SELECT * FROM DISLIKED_CONTENT;

SELECT * FROM 
CUSTOMER A 
INNER JOIN 
RECOMMENDED_CONTENT B 
ON (A.CUSTOMER_ID = B.CUSTOMER_ID)
INNER JOIN
CONTENT C
ON (B.CONTENT_ID = C.CONTENT_ID)
LEFT OUTER JOIN
DISLIKED_CONTENT D
ON (D.CUSTOMER_ID = B.CUSTOMER_ID AND D.CONTENT_ID = B.CONTENT_ID)
WHERE A.CUSTOMER_ID = '000001' AND D.CONTENT_ID IS NULL;

```