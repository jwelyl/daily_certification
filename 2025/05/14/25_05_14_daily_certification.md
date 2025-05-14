# 25_05_14_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 15509 **Xayahh-Rakann at Moloco (Hard)**

[15509번: Xayahh-Rakann at Moloco (Hard)](http://boj.ma/15509/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX_D = 5;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	private static int K;
	
	private static char[][] board;
	
	private static final Map<String, Integer> map = new HashMap<>();
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		board = new char[N][];
		for(int y = 0; y < N; y++)
			board[y] = br.readLine().toCharArray();
	
		for(int y = 0; y < N; y++) {
			for(int x = 0; x < M; x++)
				dfs(y, x, 0, new char[MAX_D]);
		}
	
		for(int q = 0; q < K; q++)
			sb.append(map.getOrDefault(br.readLine(), 0)).append("\n");
	
		System.out.print(sb);
	}	//	main-end
	
	private static void dfs(int y, int x, int depth, char[] strArr) {
		if(depth == MAX_D)
			return;
		
		strArr[depth] = board[y][x];
		StringBuilder sb = new StringBuilder();
		for(int i = 0; i <= depth; i++)
			sb.append(strArr[i]);
		String str = sb.toString();
		
		map.put(str, map.getOrDefault(str, 0) + 1);
		
		for(int d = 0; d < 8; d++) {
			int ny = (y + dy[d] + N) % N;
			int nx = (x + dx[d] + M) % M;
			
			dfs(ny, nx, depth + 1, strArr);
		}
	}
	
	private static final int[] dy = {0, 1, 1, 1, 0, -1, -1, -1};
	private static final int[] dx = {1, 1, 0, -1, -1, -1, 0, 1};	
}	//	Main-class-end
```
```sql

create table employee (
	empl_id int primary key,
  	empl_name varchar(200),
  	salary int,
  	dept_id int,
  	job_id varchar(2)
);

create table department (
	dept_id int primary key,
  	dept_name varchar(200),
  	region varchar(200)
);

create table hr (
	dept_id int,
  	job_id varchar(2),
  	primary key(dept_id, job_id)
);

insert into employee (empl_id, empl_name, salary, dept_id, job_id)
values (101, 'Jane', 5000, 10, 'J1'),
	   (102, 'Charles', 6000, 20, 'J2'),
       (103, 'Zzit', 4000, 10, 'J1'),
       (104, 'Hang', 7000, 30, 'J3');
       
insert into department (dept_id, dept_name, region)
values (10, 'Design', 'Seoul'),
	   (20, 'Develop', 'Busan'),
       (30, 'Marketing', 'Seoul');
       
insert into hr (dept_id, job_id)
values (10, 'J1'),
       (30, 'J3');
       
select empl_name, salary 
from employee
where salary > (select avg(salary) from employee);

select empl_name
from employee
where (dept_id, job_id)
in (select dept_id, job_id from hr);
       
select empl_name
from employee
where dept_id in (select dept_id from department where region = 'Seoul');

select empl_name, (select dept_name from department D where D.dept_id = E.dept_id) as department_name
from employee E;

select e.empl_name, d.dept_name
from employee e, (select dept_id, dept_name from department where region = 'Seoul') d
where e.dept_id = d.dept_id;

select e1.empl_name, e1.salary from employee e1
where e1.salary < (select max(e2.salary) from employee e2 where e2.dept_id = e1.dept_id);
```