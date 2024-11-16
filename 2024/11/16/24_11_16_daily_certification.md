# 24_11_16_daily_certification

# Database

# Normalization

## Initial Table

| **bank_name** | **account_num** | **account_id** | **class** | **ratio** | **empl_id** | **empl_name** | **card_id** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Woori | 010-9231-1121 | A11 | BRONZE | 0.1 | E1 | SONY | C101 |
| Woori | 102-992-180125 | A12 | SILVER | 0.2 | E1 | SONY | C102 |
| Kookmin | 010-9231-1121 | A13 | LOYAL | 0.7 | E1 | SONY | C103 |
| Kookmin | 010-1221-1732 | A21 | LOYAL | 1 | E2 | MESSI | C201
C202 |

**class** 

Woori (BRONZE, SILVER, GOLD)

Kookmin (STAR, PRESTIGE, LOYAL)

**Key**

{account_id}

{bank_name, account_num}

## 1NF

모든 attribute의 value는 나눠질 수 없는 단일한 값이어야 한다.

### **Before**

| **bank_name** | **account_num** | **account_id** | **class** | **ratio** | **empl_id** | **empl_name** | **card_id** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Woori | 010-9231-1121 | A11 | BRONZE | 0.1 | E1 | SONY | C101 |
| Woori | 102-992-180125 | A12 | SILVER | 0.2 | E1 | SONY | C102 |
| Kookmin | 010-9231-1121 | A13 | LOYAL | 0.7 | E1 | SONY | C103 |
| Kookmin | 010-1221-1732 | A21 | LOYAL | 1 | E2 | MESSI | C201
C202 |

### **After**

| **bank_name** | **account_num** | **account_id** | **class** | **ratio** | **empl_id** | **empl_name** | **card_id** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Woori | 010-9231-1121 | A11 | BRONZE | 0.1 | E1 | SONY | C101 |
| Woori | 102-992-180125 | A12 | SILVER | 0.2 | E1 | SONY | C102 |
| Kookmin | 010-9231-1121 | A13 | LOYAL | 0.7 | E1 | SONY | C103 |
| Kookmin | 010-1221-1732 | A21 | LOYAL | 1 | E2 | MESSI | C201 |
| Kookmin | 010-1221-1732 | A21 | LOYAL | 1 | E2 | MESSI | C202 |

**Key**

{account_id, card_id}

{bank_name, account_num, card_id}

**Non-prime attribute**

class, ratio, empl_id, empl_name

### 2NF

모든 Non-prime attribute는 Key에 대해 Fully Dependent해야 한다.

### **Before**

| **bank_name** | **account_num** | **account_id** | **class** | **ratio** | **empl_id** | **empl_name** | **card_id** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Woori | 010-9231-1121 | A11 | BRONZE | 0.1 | E1 | SONY | C101 |
| Woori | 102-992-180125 | A12 | SILVER | 0.2 | E1 | SONY | C102 |
| Kookmin | 010-9231-1121 | A13 | LOYAL | 0.7 | E1 | SONY | C103 |
| Kookmin | 010-1221-1732 | A21 | LOYAL | 1 | E2 | MESSI | C201 |
| Kookmin | 010-1221-1732 | A21 | LOYAL | 1 | E2 | MESSI | C202 |

**Key**

{account_id, card_id}

{bank_name, account_num, card_id}

**Functional Dependencies**

{account_id, card_id} → {class, ratio, empl_id, empl_name}

{bank_name, account_num, card_id} → {class, ratio, empl_id, empl_name}

{account_id, card_id} → {class, ratio, empl_id, empl_name}에서 사실 {account_id} →  {class, ratio, empl_id, empl_name}이다.

{bank_name, account_num, card_id} → {class, ratio, empl_id, empl_name}에서 사실 {bank_name, account_num} →  {class, ratio, empl_id, empl_name}이다.

### **After**

| **account_id** | **card_id** |
| --- | --- |
| A11 | C101 |
| A12 | C102 |
| A13 | C103 |
| A21 | C201 |
| A21 | C202 |

**Key**

{card_id}

**Functional Dependencies**

{card_id} → {account_id}

| **bank_name** | **account_num** | **account_id** | **class** | **ratio** | **empl_id** | **empl_name** |
| --- | --- | --- | --- | --- | --- | --- |
| Woori | 010-9231-1121 | A11 | BRONZE | 0.1 | E1 | SONY |
| Woori | 102-992-180125 | A12 | SILVER | 0.2 | E1 | SONY |
| Kookmin | 010-9231-1121 | A13 | LOYAL | 0.7 | E1 | SONY |
| Kookmin | 010-1221-1732 | A21 | LOYAL | 1 | E2 | MESSI |

**Key**

{account_id}

{bank_name, account_num}

**Functional Dependencies**

{account_id} → {class, ratio, empl_id, empl_name}

{bank_name, account_num} → {class, ratio, empl_id, empl_name}

{class} → {bank_name}

{empl_id} → {empl_num}

## 3NF

non-prime attribute 사이에 FD가 존재해서는 안된다.

### **Before**

| **account_id** | **card_id** |
| --- | --- |
| A11 | C101 |
| A12 | C102 |
| A13 | C103 |
| A21 | C201 |
| A21 | C202 |

**Key**

{card_id}

**Functional Dependencies**

{card_id} → {account_id}

| **bank_name** | **account_num** | **account_id** | **class** | **ratio** | **empl_id** | **empl_name** |
| --- | --- | --- | --- | --- | --- | --- |
| Woori | 010-9231-1121 | A11 | BRONZE | 0.1 | E1 | SONY |
| Woori | 102-992-180125 | A12 | SILVER | 0.2 | E1 | SONY |
| Kookmin | 010-9231-1121 | A13 | LOYAL | 0.7 | E1 | SONY |
| Kookmin | 010-1221-1732 | A21 | LOYAL | 1 | E2 | MESSI |

**Key**

{account_id}

{bank_name, account_num}

**Functional Dependencies**

{account_id} → {class, ratio, empl_id, empl_name}

{bank_name, account_num} → {class, ratio, empl_id, empl_name}

{class} → {bank_name}

{empl_id} → {empl_num}

**Transitivie Functional Dependencies**

{account_id} → {empl_id}, {empl_id} → {empl_name} then {account_id} 

{bank_name, account_num} → {empl_id}, {empl_id} → {empl_name} then {bank_name, account_num} → {empl_name}

### After

| **account_id** | **card_id** |
| --- | --- |
| A11 | C101 |
| A12 | C102 |
| A13 | C103 |
| A21 | C201 |
| A21 | C202 |

**Key**

{card_id}

**Functional Dependencies**

{card_id} → {account_id}

| **empl_id** | **empl_name** |
| --- | --- |
| E1 | SONY |
| E2 | MESSI |

**Key**

{empl_id}

**Functional Dependencies**

{empl_id} → {empl_name}

| **bank_name** | **account_num** | **account_id** | **class** | **ratio** | **empl_id** |
| --- | --- | --- | --- | --- | --- |
| Woori | 010-9231-1121 | A11 | BRONZE | 0.1 | E1 |
| Woori | 102-992-180125 | A12 | SILVER | 0.2 | E1 |
| Kookmin | 010-9231-1121 | A13 | LOYAL | 0.7 | E1 |
| Kookmin | 010-1221-1732 | A21 | LOYAL | 1 | E2 |

**Key**

{account_id}

{bank_name, account_num}

**Functional Dependencies**

{account_id} → {class, ratio, empl_id}

{bank_name, account_num} → {class, ratio, empl_id}

{class} → {bank_name}

## BCNF

모든 유효한 Non-trivial FD X -> Y에서 X는 super key여야 한다.

### Before

| **account_id** | **card_id** |
| --- | --- |
| A11 | C101 |
| A12 | C102 |
| A13 | C103 |
| A21 | C201 |
| A21 | C202 |

**Key**

{card_id}

**Functional Dependencies**

{card_id} → {account_id}

| **empl_id** | **empl_name** |
| --- | --- |
| E1 | SONY |
| E2 | MESSI |

**Key**

{empl_id}

**Functional Dependencies**

{empl_id} → {empl_name}

| **bank_name** | **account_num** | **account_id** | **class** | **ratio** | **empl_id** |
| --- | --- | --- | --- | --- | --- |
| Woori | 010-9231-1121 | A11 | BRONZE | 0.1 | E1 |
| Woori | 102-992-180125 | A12 | SILVER | 0.2 | E1 |
| Kookmin | 010-9231-1121 | A13 | LOYAL | 0.7 | E1 |
| Kookmin | 010-1221-1732 | A21 | LOYAL | 1 | E2 |

**Key**

{account_id}

{bank_name, account_num}

**Functional Dependencies**

{account_id} → {class, ratio, empl_id}

{bank_name, account_num} → {class, ratio, empl_id}

{class} → {bank_name}

{class}는 Super Key가 아니고 {class} → {bank_name}은 Non-trivial FD임

### After

| **account_id** | **card_id** |
| --- | --- |
| A11 | C101 |
| A12 | C102 |
| A13 | C103 |
| A21 | C201 |
| A21 | C202 |

**Key**

{card_id}

**Functional Dependencies**

{card_id} → {account_id}

| **empl_id** | **empl_name** |
| --- | --- |
| E1 | SONY |
| E2 | MESSI |

**Key**

{empl_id}

**Functional Dependencies**

{empl_id} → {empl_name}

| **class** | **bank_name** |
| --- | --- |
| BRONZE | Woori |
| SILVER | Woori |
| GOLD | Woori |
| STAR | Kookmin |
| PRESTIGE | Kookmin |
| LOYAL | Kookmin |

**Key**

{class}

**Functional Dependencies**

{class} → {bank_name}

| **account_num** | **account_id** | **class** | **ratio** | **empl_id** |
| --- | --- | --- | --- | --- |
| 010-9231-1121 | A11 | BRONZE | 0.1 | E1 |
| 102-992-180125 | A12 | SILVER | 0.2 | E1 |
| 010-9231-1121 | A13 | LOYAL | 0.7 | E1 |
| 010-1221-1732 | A21 | LOYAL | 1 | E2 |

**Key**

{account_id}

**Functional Dependencies**

{account_id} → {account_num, class, ratio, empl_id}

# Problem Solving (Algorithm & SQL)

### **BOJ 2917** 늑대 사냥꾼

[2917번: 늑대 사냥꾼](https://boj.ma/2917/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.LinkedList;
import java.util.PriorityQueue;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	
	private static char[][] map;	//	맵
	private static int[][] dist;	//	dist[y][x] : (y, x)에서 가장 가까운 나무까지의 거리
	
	private static int sy;
	private static int sx;
	private static int maxDist = 0;
	
	private static final PriorityQueue<int[]> pq = new PriorityQueue<>((n1, n2) -> Integer.compare(n1[2], n2[2]));

	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		map = new char[N][M];
		dist = new int[N][M];
		for(int y = 0; y < N; y++) {
			map[y] = br.readLine().toCharArray();
			Arrays.fill(dist[y], 500 * 500 + 1);
			
			for(int x = 0; x < M; x++) {
				if(map[y][x] == '+') {
					dist[y][x] = 0;
					pq.offer(new int[] {y, x, dist[y][x]});
				}
				else if(map[y][x] == 'V') {
					sy = y;
					sx = x;
				}
			}
		}
		
		dijkstra();
		
		System.out.println(parametricSearch());
	}	//	main-end
	
	private static int parametricSearch() {
		int start = 0;
		int end = maxDist;
		int res = -1;
		
		while(start <= end) {
			int mid = (start + end) / 2;
			
			if(bfs(mid)) {	//	나무로부터 최단거리가 mid 이상인 칸들로만 V에서 J로 갈 수 있을 경우
				res = mid;
				start = mid + 1;
			}
			else
				end = mid - 1;
		}
		
		return res;
	}
	
	private static boolean bfs(int minDist) {
		if(dist[sy][sx] < minDist)
			return false;
		
		Queue<int[]> q = new LinkedList<>();
		boolean[][] visited = new boolean[N][M];
		visited[sy][sx] = true;
		q.offer(new int[] {sy, sx});
		
		while(!q.isEmpty()) {
			int[] cur = q.poll();
			int cy = cur[0];
			int cx = cur[1];
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				
				if(isIn(ny, nx) && dist[ny][nx] >= minDist && !visited[ny][nx]) {
					if(map[ny][nx] == 'J')
						return true;
					
					visited[ny][nx] = true;
					q.offer(new int[] {ny, nx});
				}
			}
		}
		
		return false;
	}
	
	private static void dijkstra() {
		while(!pq.isEmpty()) {
			int[] cur = pq.poll();
			int cy = cur[0];
			int cx = cur[1];
			int cc = cur[2];
			
			if(dist[cy][cx] < cc)
				continue;
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				int nc = cc + 1;
				
				if(isIn(ny, nx) && nc < dist[ny][nx]) {
					dist[ny][nx] = nc;
					maxDist = Math.max(maxDist, dist[ny][nx]);
					pq.offer(new int[] {ny, nx, dist[ny][nx]});
				}
			}
		}
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}
}	//	Main-class-end
```