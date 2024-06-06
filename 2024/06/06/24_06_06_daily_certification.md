# 24_06_06_daily_certificatio

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 번호표를 든 N명의 사람**

[https://www.codetree.ai/missions/8/problems/n-people-with-numbers/description](https://www.codetree.ai/missions/8/problems/n-people-with-numbers/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/n-people-with-numbers/description)

**코드** 

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;
    private static int tMax;
    private static int[] d;
    
    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        tMax = Integer.parseInt(tokens.nextToken());

        d = new int[n + 1];
        for(int i = 1; i <= n; i++)
            d[i] = Integer.parseInt(br.readLine());

        System.out.println(parametricSearch());
    }   //  main-end

    private static int parametricSearch() {
        int start = 0;
        int end = n;
        int ret = Integer.MAX_VALUE;

        while(start <= end) {
            int mid = (start + end) / 2;    //  한 번에 mid명 올림

            if(isOk(mid)) { //  한 번에 mid명씩 올렸을 때 모든 사람이 무대를 끝내는데 tMax 시간 이하면
                ret = mid;
                end = mid - 1;  //  한 번에 올리는 사람 수를 더 적게 해도 가능한지 체크
            }
            else
                start = mid + 1;    //  한 번에 더 많은 사람을 올려야 함
        }

        return ret;
    }

    //  k명씩 무대에 올렸을 때 tMax 시간 안에 모두 무대를 마칠 수 있으면 true
    private static boolean isOk(int k) {
        PriorityQueue<Integer> pq = new PriorityQueue<>();  //  무대 끝나는 시간이 빠른 순서대로

        int curTime = 0;
        int idx = 1;    //  올라갈 사람 번호

        for(; idx <= k; idx++)
            pq.offer(curTime + d[idx]);   //  k명의 사람 무대에 올림

        while(!pq.isEmpty()) {
            curTime = pq.poll();    //  무대 위의 사람 중 가장 먼저 무대 마친 사람이 내려온 시간

            if(idx <= n)  //  아직 무대에 올라야 할 사람이 남은 경우
                pq.offer(curTime + d[idx++]);
        }

        return curTime <= tMax; //  마지막으로 무대 마친 사람이 tMax가 지나기 전에 내려올 수 있으면 true
    }
}   //  Main-class-end
```

**BOJ_15684 사다리 조작**

[](https://www.acmicpc.net/problem/15684)

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
	
	private static int n;	//	세로선 개수 n개
	private static int m;	//	초기 주어지는 가로선 수 m개
	private static int h;	//	가로선 놓을 수 있는 점선 h개
	
	private static boolean[][] ladder;	//	ladder[h][w] : h행의 w에서 w + 1로 가는 가로선 존재하면 true
	
	private static final List<Pos> posList = new ArrayList<>();	//	가로선 추가로 놓을 수 있는 후보
	
	private static boolean ok = false;	//	0개 이상의 가로선을 추가해서 각 세로선에서 시작했을 때 시작할 세로선에서 끝날 수 있으면 true
	
	private static int ans = -1;
	
	public static void main(String[] args) throws IOException {
		tokens = new StringTokenizer(br.readLine());
		n = Integer.parseInt(tokens.nextToken());
		m = Integer.parseInt(tokens.nextToken());
		h = Integer.parseInt(tokens.nextToken());
		
		ladder = new boolean[h + 1][n];
		
		for(int i = 0; i < m; i++) {
			tokens = new StringTokenizer(br.readLine());
			int a = Integer.parseInt(tokens.nextToken());
			int b = Integer.parseInt(tokens.nextToken());
			
			ladder[a][b] = true;
		}

		//	가로선 추가로 놓을 수 있는 위치 찾기
		for(int row = 1; row <= h; row++) {
			for(int col = 1; col < n; col++) {
				if(ladder[row][col])	//	row행의 col선과 col+1선을 연결하는 가로선이 이미 놓여져 있으면 skip
					continue;
			
				//	row행의 col선과 col+1선을 연결하는 가로선의 왼쪽 가로선 또는 오른쪽 가로선이 이미 놓여 있으면 skip
				if((col >= 2 && ladder[row][col - 1]) || (col < n - 1 && ladder[row][col + 1]))
					continue;
				
				posList.add(new Pos(row, col));
			}
		}

		if(!simulation()) {	//	가로선 추가 없이는 안될 경우
			for(int cnt = 1; cnt <= 3; cnt++) {	//	cnt개의 가로선을 추가함
				combination(1, cnt, 0);	//	cnt개의 가로선 추가해봄
				
				if(ok) {	//	cnt개 가로선 추가했더니 될 경우
					ans = cnt;
					break;
				}
			}
		}
		else ans = 0;	//	가로선 추가 없이 될 경우
		
		System.out.println(ans);
		//	가로선 추가
	}	//	main-end
	
	private static void combination(int nth, int cnt, int start) {
		if(ok)	//	이미 최소 가로선 추가해서 되는 경우를 찾았으면
			return;
	
		for(int i = start; i < posList.size(); i++) {
			Pos pos = posList.get(i);	//	i번째 점
			int row = pos.row;
			int col = pos.col;
			
			if((col - 1 >= 1 && ladder[row][col - 1]) || (col + 1 < n && ladder[row][col + 1]))
				continue;	//	현재 가로선 추가 불가능하므로 다음 가로선으로
				
			ladder[row][col] = true;	//	현재 가로선 추가 가능하므로 추가
			
			if(nth == cnt) {	//	cnt개의 가로선 모두 놓았을 경우
				if(simulation()) {
					ok = true;
					return;
				}
			}
			else	//	더 놔야 할 경우
				combination(nth + 1, cnt, i + 1);
			
			ladder[row][col] = false;	//	현재 가로선 다시 제거
		}
	}
	
	private static boolean simulation() {
		for(int i = 1; i <= n; i++) {
			if(simulation(i) != i)	//	i번째 세로선에서 출발해서 도착한 세로선이 i가 아닐 경우
				return false;
		}
		
		return true;
	}
	
	//	col번째 세로선에서 ladder를 타고 내려왔을 때 도착하는 세로선
	private static int simulation(int col) {
		int nowRow = 1;
		int nowCol = col;
		
		while(nowRow <= h) {
			int left = nowCol - 1;		//	left - nowCol 연결 가로선 있나 확인
			
			if(left >= 1 && ladder[nowRow][left])	//	nowCol에서 left로 갈 수 있을 경우
				nowCol = left;	//	왼쪽으로 이동
			else if(nowCol < n && ladder[nowRow][nowCol])	//	nowCol에서 nowCol + 1로 이동할 수 있을 경우
				nowCol++;	//	오른쪽으로 이동
			
			nowRow++;
		}
		
		return nowCol;
	}
	
	private static class Pos {
		int row;	//	가로선 놓을 행
		int col;	//	가로선 놓을 열 (col, col + 1)을 연결
		
		public Pos(int row, int col) {
			this.row = row;
			this.col = col;
		}
	}
}	//	Main-class-end
```