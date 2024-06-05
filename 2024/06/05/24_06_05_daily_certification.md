# 24_06_05_daily_certification

# 계획

[해야 할 일](https://www.notion.so/b734cabaef734383859085787704ffab?pvs=21)

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 가장 가까운 두 점 사이의 거리를 최대화하기**

[https://www.codetree.ai/missions/8/problems/maximize-dist-of-nearest-points/description](https://www.codetree.ai/missions/8/problems/maximize-dist-of-nearest-points/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/maximize-dist-of-nearest-points/description)

**코드** 

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;
    private static final List<Segment> segments = new ArrayList<>();

    private static int left = Integer.MAX_VALUE;
    private static int right = Integer.MIN_VALUE;

    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());

        for(int i = 0; i < n; i++) {
            tokens = new StringTokenizer(br.readLine());
            int start = Integer.parseInt(tokens.nextToken());
            int end = Integer.parseInt(tokens.nextToken());

            left = Math.min(left, start);
            right = Math.max(right, end);

            segments.add(new Segment(start, end));
        }

        Collections.sort(segments); //  선분 정렬

        System.out.println(parametricSearch());
    }   //  main-end

    private static int parametricSearch() {
        int start = 0;
        int end = right - left;
        int ret = 0;

        while(start <= end) {
            int mid = (start + end) / 2;    //  각 선분 위의 한 개씩 위치한 점들 사이의 거리를 최소 mid가 되게 놓을 수 있나 체크

            if(canPut(mid)) {   //  최소 거리 mid를 유지하며 놓을 수 있을 경우
                ret = mid;
                start = mid + 1;    //  최소 거리를 증가시켰을 때 놓을 수 있나 확인
            }
            else end = mid - 1; //  최소 거리를 더 줄여야 함
        }

        return ret;
    }

    //  각 점들 사이의 거리를 최소 dist로 유지할 수 있으면 true
    private static boolean canPut(int dist) {
        int pos = segments.get(0).start;    //  가능한 선분의 가장 왼쪽에 놓는 것이 좋음

        for(int i = 1; i < n; i++) {
            Segment segment = segments.get(i);
            int start = segment.start;  //  다음 선분 시작점
            int end = segment.end;      //  다음 선분 끝점

            int nextPos = pos + dist;   //  최소 간격 유지하며 다음 점을 놓을 수 있는 가장 작은 좌표
            if(nextPos > end)   //  다음 점을 놓을 수 있는 가장 작은 좌표가 다음 선분보다 오른쪽에 있을 경우
                return false;   //  최소 거리 dist를 유지하며 점을 놓을 수 없음

            if(nextPos < start)    //  가장 작은 좌표가 다음 선분 이전에 있을 경우
                nextPos = start;   //  다음 선분의 시작점에 놓기

            pos = nextPos; 
        }

        return true;
    }

    private static class Segment implements Comparable<Segment> {
        int start;
        int end;

        public Segment(int start, int end) {
            this.start = start;
            this.end = end;
        }

        @Override
        public int compareTo(Segment segment) {
            int ret = Integer.compare(this.start, segment.start);   //  시작점이 작은 선분이 앞으로 오도록 정렬

            if(ret == 0)
                ret = Integer.compare(this.end, segment.end);   //  시작점이 같을 경우 끝 점이 작은 선분이 앞으로 오도록 정렬

            return ret;
        }
    }
}   //  Main-class-end
```

**BOJ_17140 이차원 배열과 연산**

[](https://www.acmicpc.net/problem/17140)

**코드**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer tokens;
	
	private static int row = 3;	//	arr 행 개수
	private static int col = 3;	//	arr 열 개수
		
	private static int[][] arr;
	
	private static int r = 0;
	private static int c = 0;
	private static int k = 0;
	
	private static int time = 0;
	
	private static int ans = -1;
	
	public static void main(String[] args) throws IOException {
		arr = new int[row][col];
		
		tokens = new StringTokenizer(br.readLine());
		r = Integer.parseInt(tokens.nextToken());
		c = Integer.parseInt(tokens.nextToken());
		k = Integer.parseInt(tokens.nextToken());
		
		for(int i = 0; i < row; i++) {
			tokens = new StringTokenizer(br.readLine());
			for(int j = 0; j < col; j++)
				arr[i][j] = Integer.parseInt(tokens.nextToken());
		}
		
		do {
			if(isIn(r - 1, c - 1) && arr[r - 1][c - 1] == k) {
				ans = time;
				break;
			}
			
			time++;
			
			if(row >= col)
				simulationR();
			else
				simulationC();
		} while(time <= 100);
		
		System.out.println(ans);
	}	//	main-end
	
	private static void simulationR() {
		int newCol = 0;	//	연산 후 배열의 열 크기
		
		List<Num>[] rowNumLists = new ArrayList[row];
		List<Integer>[] rowNums = new ArrayList[row];
		
		for(int r = 0; r < row; r++) {
			rowNumLists[r] = new ArrayList<>();
			rowNums[r] = new ArrayList<>();
		}
		
		for(int r = 0; r < row; r++) {	//	각 행에 대해서
			int[] cnts = new int[101];	//	r행에 있는 각 수의 개수, cnts[num] = k면, r행에 num이 k번 나온다는 뜻
			
			for(int c = 0; c < col; c++)
				cnts[arr[r][c]]++;	//	행의 각 수가 나타난 횟수 세기
			
			for(int num = 1; num <= 100; num++)
				if(cnts[num] >= 1)
					rowNumLists[r].add(new Num(num, cnts[num]));
			
			Collections.sort(rowNumLists[r]);
			
			int colSize = rowNumLists[r].size() > 50 ? 50 : rowNumLists[r].size();
			
			for(int c = 0; c < colSize; c++) {
				Num nObj = rowNumLists[r].get(c);
				rowNums[r].add(nObj.value);
				rowNums[r].add(nObj.cnt);
			}
			
			newCol = Math.max(newCol, colSize * 2); 
		}
		
		col = newCol;
		arr = new int[row][col];
		
		for(int r = 0; r < row; r++) {
			for(int c = 0; c < rowNums[r].size(); c++)
				arr[r][c] = rowNums[r].get(c);
		}
	}
	
	private static void simulationC() {
		int newRow = 0;	//	연산 후 배열의 행 크기
		
		List<Num>[] colNumLists = new ArrayList[col];
		List<Integer>[] colNums = new ArrayList[col];
		
		for(int c = 0; c < col; c++) {
			colNumLists[c] = new ArrayList<>();
			colNums[c] = new ArrayList<>();
		}
		
		for(int c = 0; c < col; c++) {	//	각 열에 대해서
			int[] cnts = new int[101];	//	c열에 있는 각 수의 개수, cnts[num] = k면, c열에 num이 k번 나온다는 뜻
			
			for(int r = 0; r < row; r++)
				cnts[arr[r][c]]++;	//	행의 각 수가 나타난 횟수 세기
			
			for(int num = 1; num <= 100; num++) {
				if(cnts[num] >= 1)
					colNumLists[c].add(new Num(num, cnts[num]));
			}
			
			Collections.sort(colNumLists[c]);
			
			int rowSize = colNumLists[c].size() > 50 ? 50 : colNumLists[c].size();
			
			for(int r = 0; r < rowSize; r++) {
				Num nObj = colNumLists[c].get(r);
				colNums[c].add(nObj.value);
				colNums[c].add(nObj.cnt);
			}
			
			newRow = Math.max(newRow, rowSize * 2); 
		}
		
		row = newRow;
		arr = new int[row][col];
		
		for(int c = 0; c < col; c++) {
			for(int r = 0; r < colNums[c].size(); r++)
				arr[r][c] = colNums[c].get(r);
		}
	}
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < row) && (0 <= x && x < col);
	}
	
	private static class Num implements Comparable<Num> {
		int value;	//	수의 값
		int cnt;	//	수의 등장 횟수
		
		public Num(int value, int cnt) {
			this.value = value;
			this.cnt = cnt;
		}
		
		@Override
		public int compareTo(Num o) {
			int ret = Integer.compare(this.cnt, o.cnt);	//	수의 등장 횟수로 오름차순 정렬
			
			if(ret == 0)	//	수의 등장 횟수가 같을 경우
				ret = Integer.compare(this.value, o.value);	//	수의 크기로 오름차순 정렬	
			
			return ret;
		}
		
		@Override
		public String toString() {
			// TODO Auto-generated method stub
			return "Num(" + this.value + "," + this.cnt + ")";
		}
	}
}	//	Main-class-end
```