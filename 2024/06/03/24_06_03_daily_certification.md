# 24_06_03_daily_certification

# GIT

### **cherry-pick**

**cherry-pick할 commit 내역 확인**

두 브랜치 A, B가 존재할 때 B에는 존재하지만 A에는 존재하지 않는 commit 내역 확인

```bash
git log A..B
```

**cherry-pick하기**

A에 존재하지 않는 B의 commit의 해시값이 오래된 순으로 h1, h2, h3, …, hn일 때 cherry-pick하려면

```bash
git cherry-pick h1
git cherry-pick h2
...
git cherry-pick hn
```

하거나

```bash
git cherry-pick h1^..hn
```

하면 된다.

**cherry-pick 충돌 해결**

cherry-pick 과정에서 충돌 발생 시, 충돌을 해결한 후

```bash
git add 충돌 발생한 파일
git cherry-pick --continue. # cherry-pick 계속한다.
```

계속할 수 있다. 만약 cherry-pick을 취소하려면

```bash
git cherry-pick --abort
```

cherry-pick을 중단하고 이전 상태로 돌아갈 수 있다.

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 양수 직사각형의 최대 크기**

[https://www.codetree.ai/missions/2/problems/max-area-of-positive-rectangle/description](https://www.codetree.ai/missions/2/problems/max-area-of-positive-rectangle/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/2/problems/max-area-of-positive-rectangle/description)

**코드** 

```java
import java.util.*;
import java.io.*;

public class Main {
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static StringTokenizer tokens;
  private static int n, m;
  private static int[][] nums;
  
  private static int maxSize = -1;

  public static void main(String[] args) throws IOException {
    tokens = new StringTokenizer(br.readLine());
    n = Integer.parseInt(tokens.nextToken());
    m = Integer.parseInt(tokens.nextToken());

    nums = new int[n][m];
    
    for(int i = 0; i < n; i++) {
      tokens = new StringTokenizer(br.readLine());
      for(int j = 0; j < m; j++) 
        nums[i][j] = Integer.parseInt(tokens.nextToken());
    }
    
    for(int y1 = 0; y1 < n; y1++) {
    	for(int x1 = 0; x1 < m; x1++) {
    		for(int y2 = y1; y2 < n; y2++) {
    			for(int x2 = x1; x2 < m; x2++) {
    				if(isPositive(y1, x1, y2, x2))
    					maxSize = Math.max(maxSize, (y2 - y1 + 1) * (x2 - x1 + 1));
    			}
    		}
    	}
    }
    
    
    System.out.println(maxSize);
  } //  main-end
  
  private static boolean isPositive(int y1, int x1, int y2, int x2) {
	  for(int i = y1; i <= y2; i++) {
		  for(int j = x1; j <= x2; j++) {
			  if(nums[i][j] <= 0)
				  return false;
		  }
	  }
	  
	  return true;
  }
} //  Main-class-end
```

**BOJ_15685 드래곤 커브**

[](https://www.acmicpc.net/problem/15685)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static StringTokenizer tokens;
  
  private static final boolean[][] dragonCurve = new boolean[101][101];
  private static int n;
  private static int ans = 0;

  public static void main(String[] args) throws IOException {
    n = Integer.parseInt(br.readLine());

    for(int i = 0; i < n; i++) {
    	tokens = new StringTokenizer(br.readLine());
    	int x = Integer.parseInt(tokens.nextToken());
    	int y = Integer.parseInt(tokens.nextToken());
    	int d = Integer.parseInt(tokens.nextToken());
    	int g = Integer.parseInt(tokens.nextToken());
    	
    	int[] start = {x, y};
    	int[] end = {x, y};
    	
    	switch(d) {
    	case 0:
    		end[0]++;
    		break;
    	case 1:
    		end[1]--;
    		break;
    	case 2:
    		end[0]--;
    		break;
    	case 3:
    		end[1]++;
    		break;
    	}
    	
    	
    	List<int[]> posList = new ArrayList<>();
    	posList.add(start);
    	posList.add(end);
    	
    	for(int[] pos : posList)	//	드래곤 커브 좌표에 표시
    		put(pos);
    	
    	for(int j = 0; j < g; j++) {	//	다음 세대 드래곤 커브로 발전시키기
    		int size = posList.size();	//	현재 세대 드래곤 커브에 포함된 점 개수
    		
    		for(int r = 0; r < size; r++) {
    			int[] pos = posList.get(r);				//	현재 세대 드래곤 커브의 점들을
    			int[] rotated = rotate(pos, end);		//	현재 끝점을 기준으로 90도 회전시키기
    			
    			put(rotated);
    			
    			posList.add(rotated);
    		}
    	
    		end = rotate(start, end);	//	다음 세대 드래곤 커브의 끝 점은 현재 세대 시작점을 현재 세대 끝점 기준으로 90도 회전시킨 점
    		put(end);
    	}
    }
    
    for(int y = 0; y < 100; y++) {
    	for(int x = 0; x < 100; x++) {
    		if(dragonCurve[y][x] && dragonCurve[y + 1][x] && dragonCurve[y][x + 1] && dragonCurve[y + 1][x + 1])
    			ans++;
    	}
    }
    
    System.out.println(ans);
  } //  main-end

  private static boolean isIn(int[] pos) {
	  int x = pos[0];
	  int y = pos[1];
	  
	  return (0 <= x && x <= 100) && (0 <= y && y <= 100);
  }
  
  private static void put(int[] pos) {
	  if(isIn(pos))
		  dragonCurve[pos[0]][pos[1]] = true;
  }
  
  //  axis (x, y)를 중심으로 pos (a, b)를 시계방향으로 90도 회전한 점 (a', b') 반환
  private static int[] rotate(int[] pos, int[] axis) {
	  int a = pos[0];
	  int b = pos[1];
	  int x = axis[0];
	  int y = axis[1];
	  
	  return new int[]{-b + x + y, a - x + y};
  }
} //  Main-class-end
```