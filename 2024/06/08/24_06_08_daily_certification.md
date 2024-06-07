# 24_06_08_daily_certification

# Database

# Partitioning

Database 테이블을 더 작은 Table들로 나누는 것 

![Untitled](24_06_08_daily_certification%20cf2c1e33294e42e0a6b4b1571a7c3685/Untitled.png)

- Vertical Partioning
- Horizontal Partioning

## Vertical Partioning

![Untitled](24_06_08_daily_certification%20cf2c1e33294e42e0a6b4b1571a7c3685/Untitled%201.png)

Column을 기준으로 Table을 나누는 방식

테이블의 Schema가 변화한다.

### Normalization (정규화)

[Normalization](https://www.notion.so/Normalization-0fd21c574d814475bf33059d4c7766bb?pvs=21)

![Untitled](24_06_08_daily_certification%20cf2c1e33294e42e0a6b4b1571a7c3685/Untitled%202.png)

![Untitled](24_06_08_daily_certification%20cf2c1e33294e42e0a6b4b1571a7c3685/Untitled%203.png)

### Vertical Partitioning by Use Case

![Untitled](24_06_08_daily_certification%20cf2c1e33294e42e0a6b4b1571a7c3685/Untitled%204.png)

화면에서는 content 데이터는 보여줄 필요가 없다.

```sql
SELECT id, title, writer_id, created_at, read_cnt, comment_cnt
FROM article;
```

하지만 쿼리와 관계 없이 일단 Secondary Storage(HDD, SSD)에서는 Row를 통째로 가져온다.

전체 데이터를 Main Memory에서 가져온 후, 원하는 attribute만 사용한다.

따라서 사용하지도 않을 용량이 큰 content에 대한 I/O 부담이 크다.

이를 해결하기 위해 다음과 같이 테이블을 분리할 수 있다.

![Untitled](24_06_08_daily_certification%20cf2c1e33294e42e0a6b4b1571a7c3685/Untitled%205.png)

화면에서는 content 데이터는 보여줄 필요가 없다.

```sql
SELECT id, title, writer_id, created_at, read_cnt, comment_cnt
FROM article;
```

용량이 큰 content는 분리된 article_content에 존재하므로 Main Memory에 가져올 필요가 없다.

따라서 필요한 attribute들만 빠르게 Main Memory에 가져올 수 있다.

![Untitled](24_06_08_daily_certification%20cf2c1e33294e42e0a6b4b1571a7c3685/Untitled%206.png)

특정 게시글을 누르면 해당 게시글의 정보는 이미 article에서 가져와 FE에서 저장해 두었으므로 그 정보를 이용해 article_content 테이블을 조회해서 content를 가져오면 된다.

이미 정규화가 되어있는 테이블이라도 performance를 위해 Vertical Partitioning이 가능하다.

그 외에도 민감한 정보를 한 번에 가져올 수 없도록 분리하거나, 자주 사용되는 attribute와 그렇지 않은 attribute를 구분해서 Vertical Partitioning을 할 수도 있다. (전체 데이터는 JOIN으로 얻는다.)

## Horizontal Partitioning

![Untitled](24_06_08_daily_certification%20cf2c1e33294e42e0a6b4b1571a7c3685/Untitled%207.png)

Row를 기준으로 Table을 나누는 방식

테이블의 Schema는 그대로 유지된다.

![Untitled](24_06_08_daily_certification%20cf2c1e33294e42e0a6b4b1571a7c3685/Untitled%208.png)

사용자 수가 N명, 채널 수가 M명이면 최악의 경우 구독 수는 N * M이 되어 row가 N * M개가 된다.

N이 100만, M이 1000이면 row 개수는 10억개가 된다.

**테이블 크기가 커질 수록 테이블의 Index 크기도 커진다.**

**테이블에 읽기/쓰기가 있을 때마다 Index에서의 처리되는 시간도 조금씩 늘어난다.**

### Horizontal Partitioning by Hashing

![Untitled](24_06_08_daily_certification%20cf2c1e33294e42e0a6b4b1571a7c3685/Untitled%209.png)

테이블을 여러개로 분리하고, **Partition Key(user_id)의 Hash결과에 따라 분리된 테이블에 저장한다.**

**같은 schema를 가진 테이블이 여러 개 생긴다.**

![Untitled](24_06_08_daily_certification%20cf2c1e33294e42e0a6b4b1571a7c3685/Untitled%2010.png)

Partition Key로 조회할 경우, Partition Key의 결과에 따른 분리된 하나의 테이블에서 조회하면 된다.

Partition Key가 아닌 값으로 조회할 경우, 분리된 모든 테이블에 대해서 조회를 해야 한다.

**따라서 조회에 가장 많이 사용될 패턴에 따라 Partition Key를 정해야 한다.**

또한 Hash Function을 잘 정의해서 분리된 모든 테이블에 **데이터가 균등하게 분배될 수 있도록** 해야 한다.

한 번 Hash Function으로 Partion을 정하면 Partition을 추가하기가 어려우므로 처음에 잘 정해야 한다.

# Sharding

![Untitled](24_06_08_daily_certification%20cf2c1e33294e42e0a6b4b1571a7c3685/Untitled%2011.png)

Horizontal Partioning과 같이 동작한다.

**하지만 각 Partition이 독립된 Database Server에 저장된다.**

### Horizontal Partitioning

![Untitled](24_06_08_daily_certification%20cf2c1e33294e42e0a6b4b1571a7c3685/Untitled%2012.png)

하나의 DB Server에 모든 Partition에 대한 BE 서버 요청이 몰리므로, Hardware 리소스가 한정된다.

### Sharding

![Untitled](24_06_08_daily_certification%20cf2c1e33294e42e0a6b4b1571a7c3685/Untitled%2013.png)

Partition 별로 BE 서버 요청이 각기 다른 DB Server에서 처리되므로 **DB Server의 load를 분산시킬 수 있다.**

**규모가 큰 서비스, 데이터가 많이 저장되는 테이블, Traffic이 많이 몰리는 테이블에 대해 고려할 수 있다.**

Sharding에서의 Partition Key는 Shard Key, Partition은 Shard라고 한다.

# Replication

DB Server에 장애가 생겼을 때를 대비해서 원본 데이터베이스의 복제본을 가지는 보조 DB Server를 두는 것을 Replication이라고 한다.

![Untitled](24_06_08_daily_certification%20cf2c1e33294e42e0a6b4b1571a7c3685/Untitled%2014.png)

원본 DB Server에 데이터를 삽입/수정/삭제하면 복제본에서도 삽입/수정/삭제해서 Sync를 맞춘다.

![Untitled](24_06_08_daily_certification%20cf2c1e33294e42e0a6b4b1571a7c3685/Untitled%2015.png)

원본 DB Server를 Master**/Primary**/Leader라고 한다.

보조 DB Server를 Slave/**Secondary**/Replica라고 한다.

### **Replication의 장점**

- **High Availability (고가용성, HA)**
    
    ![Untitled](24_06_08_daily_certification%20cf2c1e33294e42e0a6b4b1571a7c3685/Untitled%2016.png)
    
    Primary DB Server에 장애가 발생할 경우
    
    ![Untitled](24_06_08_daily_certification%20cf2c1e33294e42e0a6b4b1571a7c3685/Untitled%2017.png)
    
    BE 서버는 빠르게 Secondary DB Server에서 read/write할 수 있도록 한다. (Fail Over)
    
- **DB Server load 분산**
    
    대부분 Query는 Read Query이다. 따라서 Primary와 Secondary가 나눠서 처리할 수 있다.
    
    ![Untitled](24_06_08_daily_certification%20cf2c1e33294e42e0a6b4b1571a7c3685/Untitled%2018.png)
    
    BE 서버의 read 요청을 분산시킬 수 있다.
    

## 정리

![Untitled](24_06_08_daily_certification%20cf2c1e33294e42e0a6b4b1571a7c3685/Untitled%2019.png)

Partitioning, Sharding, Replication의 더 자세한 내용은 따로 공부해야 한다.

# Problem Solving (Algorithm & SQL)

**BOJ 1917 정육면체 전개도**

[](https://www.acmicpc.net/problem/1917)

**코드** 

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer tokens;
	
	private static final boolean[][] data = new boolean[6][6];
	private static final boolean[][] visited = new boolean[6][6];
	
	private static final List<int[]> posList = new ArrayList<>();
	
	public static void main(String[] args) throws IOException {
		for(int tc = 0; tc < 3; tc++) {
			posList.clear();
			
			for(int i = 0; i < 6; i++) {
				tokens = new StringTokenizer(br.readLine());
				Arrays.fill(visited[i], false);
				for(int j = 0; j < 6; j++) {		
					data[i][j] = (tokens.nextToken().equals("1")); 
					if(data[i][j])
						posList.add(new int[] { i, j } );
				}
			}
			
			sb.append(func() ? "yes\n" : "no\n");
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static boolean func() {
		for(int i = 0; i < 6; i++) {	//	6개의 면에 대해서
			int y = posList.get(i)[0];
			int x = posList.get(i)[1];
			
			int add = 0;	//	현재 면에서 처음 시작한 방향으로 두 번 갔을 때 도달할 수 있는 면의 수
			
			for(int d = 0; d < 4; d++) {
				int ny = y + dy[d];
				int nx = x + dx[d];	//	d 방향으로 한 번 간 칸
				
				if(!isIn(ny, nx) || !data[ny][nx])	//	 처음 한 번 갈 수 있는 칸이 없으면, 이 회전 방향은 아님
					continue;
				
				int[] dirArr = new int[4];
				dirArr[d] = 1;	//	d 방향으로 이미 한 번 감
				add += dfs(ny, nx, d, dirArr);
			}
			
			if(add != 1) 	//	처음 시작한 방향으로 2번에 갈 수 있는 칸이 유일하지 않을 경우
				return false;
		}
		
		return true;
	}
	
	private static int dfs(int cy, int cx, int dir, int[] dirArr) {
		int res = 0;
		
		visited[cy][cx] = true;
		
		if(dirArr[dir] >= 2 && dirArr[dir] % 2 == 0) {
			visited[cy][cx] = false;
			return 1;
		}
		
		for(int d = 0; d < 4; d++) {
			int ny = cy + dy[d];
			int nx = cx + dx[d];
			
			if(isIn(ny, nx) && !visited[ny][nx] && data[ny][nx]) {
				dirArr[d]++;
				res += dfs(ny, nx, dir, dirArr);
				dirArr[d]--;
			}
		}
		
		visited[cy][cx] = false;
		
		return res;
	}
	
	private static int[] dy = {0, 1, 0, -1};
	private static int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < 6) && (0 <= x && x < 6);
	}
}	//	Main-class-end
```