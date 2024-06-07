# 24_06_07_daily_certification

# Database

# B-Tree

## B-Tree의 시간 복잡도

DB Index로 보통 B-Tree 계열 트리가 사용될까?

(B-Tree 계열 트리 : B+Tree, B*Tree)

### B-Tree 계열 트리의 시간복잡도

**N개의 데이터가 있을 때 B-Tree 계열 트리의 조회, 삽입, 삭제의 시간복잡도는 O(logN)이다.**

Average Case, Worst Case 모두 O(logN)이다.

### **B-Tree vs Self-Balancing Binary Search Tree**

![Untitled](24_06_07_daily_certification%209ee1073830e94172901551f5f5f53d18/Untitled.png)

B-Tree, BST 모두 O(logN)인데 B-Tree가 사용되는 이유?

## Secondary Storage

### Computer System

![Untitled](24_06_07_daily_certification%209ee1073830e94172901551f5f5f53d18/Untitled%201.png)

- **CPU** : 프로세스(메모리에 올라온, 실행 중인 프로그램)가 실행되는 곳
- **Memory(RAM)** : 프로세스 코드들과 코드 실행에 필요한 데이터, 결과 데이터가 상주하는 곳
- **Secondary Storage** : 프로그램(소스 파일)과 데이터가 영구적으로 저장되는 곳
    - 프로세스 실행 중에 자주 쓰이지 않는 데이터가 임시로 저장되기도 한다. (Swap)
    - **데이터베이스도 Secondary Storage에 저장된다.**

### Secondary Storage

![Untitled](24_06_07_daily_certification%209ee1073830e94172901551f5f5f53d18/Untitled%202.png)

- 데이터 처리 속도가 가장, 매우 느리다.
- 데이터를 저장하는 용량이 가장 크다.
- **데이터를 읽고 쓸 때 Block 단위로 데이터를 읽고 쓴다.**

### Block

![Untitled](24_06_07_daily_certification%209ee1073830e94172901551f5f5f53d18/Untitled%203.png)

- File System에서 데이터를 읽고 쓰는 논리적인 단위
- Block 크기는 2의 거듭제곱으로 표현된다. (4KB, 8KB, 16KB …)
- 특정 데이터에 접근하려면 해당 데이터가 포함된 Block에 접근해야 한다.
    - 당장 사용하지 않을 데이터까지 읽어올 수 있다.
    - 머지 않아 사용할 예정이라면 장점이지만, 그렇지 않다면 단점이다.

### DB와 Secondary Storage

- Database는 Secondary Storage에 저장된다.
- 대용량의 정보를 영구적으로 저장이 가능하므로 중요한 정보인 Database를 저장하기 적합하다.
- 핵심 데이터를 Main Memory에 올려놓는다.
    - Main Memory에 올린 데이터에 접근할 경우 빠른 접근이 가능하다.
    - Main Memory에 올린 데이터가 아닐 경우 Secondary Storage에서 Block 단위로 데이터를 Main Memory로 가져온다.
- **DB에서 데이터에 접근할 때 Secondary Storage에 최대한 적게 접근하는 것이 성능이 좋다.**
- **Block 단위로 데이터를 읽고 쓰므로, 연관된 데이터를 모아서 저장하면 더 효율적으로 읽고  쓸 수 있다.**

## B-Tree가 DB Index로 쓰이는 이유

아래 테이블에서 b 컬럼으로 데이터를 조회한다고 가정하자.

![Untitled](24_06_07_daily_certification%209ee1073830e94172901551f5f5f53d18/Untitled%204.png)

b에 대해서 Index를 생성해야 한다.

```sql
CREATE INDEX(b);
```

**AVL Tree, Red Black Tree와 같은 Self-Balancing BST와 B-Tree를 비교해보자.**

다음과 같이 가정하자.

- 트리의 각 노드는 서로 다른 Block에 존재
- 초기에는 루트 노드를 제외한 모든 노드는 Secondary Storage에 존재한다.
- 초기에는 모든 데이터가 Secondary Storage에 존재한다.

### AVL Tree Index(b), Red Black Tree Index(b)

RBT도 AVL Tree와 비슷하므로 AVL Tree로만 설명한다.

DB에 저장된 순서대로 AVL Tree에 삽입해 Index를 만들면 다음과 같다.

![Untitled](24_06_07_daily_certification%209ee1073830e94172901551f5f5f53d18/Untitled%205.png)

*는 실제 DB Tuple을 가리키는 포인터이다.

5를 찾는다고 가정해보자. 색칠된 노드만 Main Memory에 올라왔다는 의미이다.

![Untitled](24_06_07_daily_certification%209ee1073830e94172901551f5f5f53d18/Untitled%206.png)

5는 6보다 작으므로 왼쪽 서브트리를 탐색해야 한다. **3이 있는 노드를 Main Memory에 올린다.**

![Untitled](24_06_07_daily_certification%209ee1073830e94172901551f5f5f53d18/Untitled%207.png)

5는 3보다 크므로 오른쪽 서브트리를 탐색해야 한다. **4가 있는 노드를 Main Memory에 올린다.**

![Untitled](24_06_07_daily_certification%209ee1073830e94172901551f5f5f53d18/Untitled%208.png)

5는 4보다 크므로 오른쪽 서브트리를 탐색해야 한다. **5가 있는 노드를 Main Memory에 올린다.**

![Untitled](24_06_07_daily_certification%209ee1073830e94172901551f5f5f53d18/Untitled%209.png)

5가 찾으려는 데이터이므로, Index에서의 조회는 끝났다. 이제 포인터를 통해 실제로 데이터를 가져와야 한다.

**포인터를 통해 Secondary Storage에 있는 실제 데이터를 Main Memory에 가져온다.**

**총 4번 Secondary Storage에 접근했다.**

### 5-dim B-Tree Index(b)

DB에 저장된 순서대로 5-dim B-Tree에 삽입해 Index를 만들면 다음과 같다.

![Untitled](24_06_07_daily_certification%209ee1073830e94172901551f5f5f53d18/Untitled%2010.png)

마찬가지로 5를 찾는다고 가정해보자.

![Untitled](24_06_07_daily_certification%209ee1073830e94172901551f5f5f53d18/Untitled%2011.png)

5는 4보다 크고 8보다 작으므로 2번째 서브트리를 탐색해야 한다. 

**두 번째 자식 노드를 Main Memory에 올린다.**

![Untitled](24_06_07_daily_certification%209ee1073830e94172901551f5f5f53d18/Untitled%2012.png)

데이터 5가 존재하므로, Index에서의 조회는 끝났다. 이제 포인터를 통해 실제로 데이터를 가져와야 한다.

**포인터를 통해 Secondary Storage에 있는 실제 데이터를 Main Memory에 가져온다.**

**총 2번 Secondary Storage에 접근했다.**

### 5-dim B-Tree Index vs AVL Tree

![Untitled](24_06_07_daily_certification%209ee1073830e94172901551f5f5f53d18/Untitled%2013.png)

|  | 5-dim B-Tree | AVL Tree |
| --- | --- | --- |
| Secondary Storage 접근 횟수 | 2 | 4 |
| 노드의 데이터 수 | 2~4 | 1 |

### **Secondary Storage 접근 횟수**

5-dim B-Tree의 경우 자식 수가 최소 3개, 최대 5개이다.

AVL Tree의 경우 자식 수가 1~2개이다.

**5-dim B-Tree가 탐색 범위를 훨씬 빠르게 줄일 수 있다. (데이터를 찾을 때 탐색 범위를 빠르게 좁힐 수 있다.)**

### 노드의 데이터 수

하나의 Block에 더 많은 데이터를 가지고 있으므로, **Main Memory에 한 번에 더 많은 실제 데이터를 가져올 ㅅ 수 있다. (Block 단위에 대한 저장 공간 활용도가 더 좋다.)**

### 101-dim B-Tree

- 최대 자녀 수 : 101개 (M)
- 최대 Key 수 : 100개 (M - 1)
- 최소 자녀 수 : 51개 (floor(M / 2)
- 최소 Key 수 : 50개 (floor(M / 2) - 1)

**Best Case (모든 노드가 자식 노드를 101개 가질 때, Key를 100개 가질 때)**

![Untitled](24_06_07_daily_certification%209ee1073830e94172901551f5f5f53d18/Untitled%2014.png)

100 * (101^0 + 101^1 + 101^2 + 101^3) = 100 * (101^4 - 1) / (101 - 1) = 101^4 - 1 > 10^8

1억개가 넘는 데이터가 4개 Level(0 ~3) 안에 저장된다.

**1억개가 넘는 데이터 중에서 특정 데이터를 탐색할 때 Secondary Storage에 4번 접근으로 탐색이 가능하다.**

**Worst Case (모든 노드가 자식 노드를 51개 가질 때, Key를 50개 가질 때)** 

**(단 root는 제외, Key 1개, 자녀 2개 가능)**

![Untitled](24_06_07_daily_certification%209ee1073830e94172901551f5f5f53d18/Untitled%2015.png)

Worst Case에도 26만개가 넘는 데이터가 4개 Level(0 ~3) 안에 저장되고 접근이 가능다.

**Average Case**

![Untitled](24_06_07_daily_certification%209ee1073830e94172901551f5f5f53d18/Untitled%2016.png)

4개 Level로 못해도 26만개, 최대 1억개 이상의 데이터를 저장할 수 있고 접근할 수 있다.

AVL Tree 같은 이진트리로 26만개의 데이터를 저장하고 접근하려면 최소 19개의 Level이 필요하다.

## 정리

![Untitled](24_06_07_daily_certification%209ee1073830e94172901551f5f5f53d18/Untitled%2017.png)

### Self-Balancing BST, Hash Index

하나의 Block에 노드를 많이 넣으면 되지 않나? → 그게 결국 B-Tree임.

Hash Index는 삽입/삭제/조회가 O(1), But equality 조회만 가능, 범위 기반 검색, 정렬이 불가능함

# Problem Solving (Algorithm & SQL)

**BOJ 18808 스티커 붙이기**

[](https://www.acmicpc.net/problem/18808)

**코드** 

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer tokens;
	
	private static int n;	//	노트북 세로 길이
	private static int m;	//	노트북 가로 길이
	private static int k;	//	스티커 개수
	
	private static boolean[][] laptop;			//	노트북
	private static boolean[][][][] stickers;	//	stickers[i][j] : i번째 스티커의 90 * j도 회전 상태
	
	private static int ans = 0;
	
	public static void main(String[] args) throws IOException {
		tokens = new StringTokenizer(br.readLine());
		n = Integer.parseInt(tokens.nextToken());
		m = Integer.parseInt(tokens.nextToken());
		k = Integer.parseInt(tokens.nextToken());
		
		laptop = new boolean[n][m];
		stickers = new boolean[k][4][][];
		
		for(int i = 0; i < k; i++) {
			tokens = new StringTokenizer(br.readLine());
			int row = Integer.parseInt(tokens.nextToken());
			int col = Integer.parseInt(tokens.nextToken());
			
			stickers[i][0] = new boolean[row][col];
			
			for(int r = 0; r < row; r++) {
				tokens = new StringTokenizer(br.readLine());
				for(int c = 0; c < col; c++)
					stickers[i][0][r][c] = Integer.parseInt(tokens.nextToken()) == 1;
			}
			
			for(int rot = 1; rot < 4; rot++)
				stickers[i][rot] = rotate90(stickers[i][rot - 1]);
		}
		
		for(int num = 0; num < k; num++) {	//	num번째 스티커
			for(int rot = 0; rot < 4; rot++) {	//	90 * rot 회전시킨 상태
				boolean[][] sticker = stickers[num][rot];
				
				boolean ok = false;	//	이 스티커를 이 회전상태로 붙일 수 있으면 true
				
				OUTER:
				for(int r = 0; r < n; r++) {
					for(int c = 0; c < m; c++) {
						if(canPatch(r, c, sticker)) {	//	왼쪽 위 칸을 (r, c)로 했을 때 이 스티커를 붙일 수 있을 경우
							patch(r, c, sticker);		//	붙이기
							ok = true;
							break OUTER;
						}
					}
				}
				
				if(ok)
					break;
			}
		}
		
		for(int i = 0; i < n; i++)
			for(int j = 0; j < m; j++)
				if(laptop[i][j])
					ans++;
		
		System.out.println(ans);
	}	//	main-end
	
	private static boolean canPatch(int row, int col, boolean[][] sticker) {
		for(int i = 0; i < sticker.length; i++) {
			for(int j = 0; j < sticker[0].length; j++) {
				int r = i + row;
				int c = j + col;
				
				if(!isIn(r, c))	//	스티커가 노트북 밖으로 삐져나갈 경우
					return false;
				
				if(sticker[i][j] && laptop[r][c])	//	이미 스티커가 붙어있는 칸일 경우
					return false;
			}
		}
		
		return true;
	}
	
	private static void patch(int row, int col, boolean[][] sticker) {
		for(int i = 0; i < sticker.length; i++) {
			for(int j = 0; j < sticker[0].length; j++) {
				int r = i + row;
				int c = j + col;
				
				laptop[r][c] = laptop[r][c] | sticker[i][j];
			}
		}
	}
	
	private static boolean isIn(int r, int c) {
		return (0 <= r && r < n) && (0 <= c && c < m);
	}
	
	//	sticker를 시계방향으로 90도 회전시켜서 반환
	private static boolean[][] rotate90(boolean[][] sticker) {
		boolean[][] rotated = new boolean[sticker[0].length][sticker.length];
		
		for(int i = 0; i < sticker[0].length; i++) {
			for(int j = 0; j < sticker.length; j++)
				rotated[i][j] = sticker[sticker.length - j - 1][i];
		}
		
		return rotated;
	}
}	//	Main-class-end
```