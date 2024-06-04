# 24_06_04_daily_certification

# Problem Solving (Algorithm & SQL)

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