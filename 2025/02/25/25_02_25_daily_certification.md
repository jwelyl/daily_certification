# 25_02_25_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 26615 다오의 행사 계획하기

[26615번: 다오의 행사 계획하기](http://boj.ma/26615/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX_H = 100_001;
	private static final int MAX_A = (int)Math.ceil(Math.log(MAX_H) / Math.log(2)) + 1; 
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int N;
	private static int M;
	private static int T;
	private static int K;
	
	private static List<Integer>[] tree;
	private static int[] depth;
	
	private static int[][] ancestors;
	
	private static long[] prefixSum;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		T = Integer.parseInt(st.nextToken());
		
		tree = new ArrayList[N * M + 1];
		for(int v = 0; v <= N * M; v++)
			tree[v] = new ArrayList<>();
		
		depth = new int[N * M + 1];
		ancestors = new int[MAX_A][N * M + 1];
		prefixSum = new long[T + 2];
		
		inputTree();
		dfs(1, 0, 1);
		makeAncestors();
		
		imos();
	}	//	main-end
	
	private static int dist(int v1, int v2) {
		int deeper = depth[v1] > depth[v2] ? v1 : v2;
		int shallower = deeper == v1 ? v2 : v1;
		int diff = depth[deeper] - depth[shallower];
		int exp = 0;
		
		int lca = 0;
		
		while(diff > 0) {
			int r = diff & 1;
			diff >>= 1;
			
			if(r == 1)
				deeper = ancestors[exp][deeper];
			
			exp++;
		}
		
		if(deeper == shallower)
			lca = deeper;
		else {
			for(int i = MAX_A - 1; i >= 0; i--) {
    			int x = ancestors[i][deeper];
    			int y = ancestors[i][shallower];
    			
    			if(x != y) {
    				deeper = x;
    				shallower = y;
    			}
    		}
			
			lca = ancestors[0][deeper];
		}
        
		return depth[v1] + depth[v2] - 2 * depth[lca];
	}
	
	private static void imos() throws IOException {
		K = Integer.parseInt(br.readLine());
		for(int k = 0; k < K; k++) {
			st = new StringTokenizer(br.readLine());
			int s = Integer.parseInt(st.nextToken());
			int e = Integer.parseInt(st.nextToken());
			int a = Integer.parseInt(st.nextToken());
			int b = Integer.parseInt(st.nextToken());
			int c = Integer.parseInt(st.nextToken());
			int d = Integer.parseInt(st.nextToken());
			int v = Integer.parseInt(st.nextToken());
			
			int v1 = a * M + b + 1;
			int v2 = c * M + d + 1;
			
			int cnt = dist(v1, v2) + 1;
			
			prefixSum[s] += cnt * v;
			prefixSum[e + 1] -= cnt * v;
		}
		
		for(int v = 1; v <= T; v++) {
			prefixSum[v] += prefixSum[v - 1];
			sb.append(prefixSum[v]).append("\n");
		}
		
		System.out.print(sb);
	}
	
	private static void makeAncestors() {
		for(int h = 1; h < MAX_A; h++) {
			for(int v = 1; v <= N * M; v++)
				//	v의 2^h번째 조상은 v의 2^(h-1)번째 조상의 2^(h-1)번째 조상
				ancestors[h][v] = ancestors[h - 1][ancestors[h - 1][v]];
		}
	}
	
	private static void dfs(int vertex, int p, int d) {
		ancestors[0][vertex] = p;	//	vertex의 2^0번째 조상(부모)가 p임
		depth[vertex] = d;
		
		for(int next : tree[vertex]) {
			if(depth[next] == 0)
				dfs(next, vertex, d + 1);
		}
	}
	
	private static void inputTree() throws IOException {
		for(int y = 0; y < N - 1; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < M; x++) {
				int v1 = y * M + x + 1;
				int v2 = (y + 1) * M + x + 1;
				
				int wall = Integer.parseInt(st.nextToken());
				
				if(wall == 0) {
					tree[v1].add(v2);
					tree[v2].add(v1);
				}
			}
		}
		
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < M - 1; x++) {
				int v1 = y * M + x + 1;
				int v2 = v1 + 1;
				
				int wall = Integer.parseInt(st.nextToken());
				
				if(wall == 0) {
					tree[v1].add(v2);
					tree[v2].add(v1);
				}
			}
		}
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