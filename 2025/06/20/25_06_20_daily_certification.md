# 25_06_20_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 22866 탑 보기

[22866번: 탑 보기](http://boj.ma/22866/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Stack;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;
	
	private static int[] heights;	//	건물 높이
	
	//	건물 번호
	private static final Stack<Integer> stack = new Stack<>();
	
	//	leftCnts[i] : i번째 건물의 왼쪽 건물 중 볼 수 있는 건물 개수
	private static int[] leftCnts;
	//	lefNum[i] : i번째 건물에서 볼 수 있는 왼쪽 건물 중 가장 가까이에 있는 건물
	private static int[] leftNum;
	//	rightCnts[i] : i번째 건물의 오른쪽 건물 중 볼 수 있는 건물 개수
	private static int[] rightCnts;
	//	rightNum[i] : i번째 건물에서 볼 수 있는 오른쪽 건물 중 가장 가까이에 있는 건물
	private static int[] rightNum;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		heights = new int[N + 1];
		leftCnts = new int[N + 1];
		leftNum = new int[N + 1];
		rightCnts = new int[N + 1];
		rightNum = new int[N + 1];
		
		st = new StringTokenizer(br.readLine());
		for(int i = 1; i <= N; i++)
			heights[i] = Integer.parseInt(st.nextToken());
		
		//	cnum번째 건물에서 볼 수 있는 왼쪽 건물 개수와 가장 가까운 왼쪽 건물 찾기
		for(int cnum = 1; cnum <= N; cnum++) {
			int ch = heights[cnum];	//	현재 건물 높이
			
			while(!stack.isEmpty()) {
				int pnum = stack.peek();
				int ph = heights[pnum];	//	더 왼쪽에 있는 건물 높이
				
				if(ph > ch)	//	현재 건물보다 높은 건물이 나올때까지 제거하기
					break;
				
				stack.pop();
			}
			
			leftCnts[cnum] = stack.size();	//	stack에 남아있는 건물 개수가 현재 건물에서 볼 수 있는 건물 개수
			
			if(!stack.isEmpty())	//	볼 수 있는 건물이 있을 경우
				leftNum[cnum] = stack.peek();	//	그 중 가장 가까운 건물의 번호
			
			stack.push(cnum);
		}
		
		stack.clear();
		
		//	cnum번째 건물에서 볼 수 있는 오른쪽 건물 개수와 가장 가까운 오른쪽 건물 찾기
		for(int cnum = N; cnum >= 1; cnum--) {
			int ch = heights[cnum];	//	현재 건물 높이
			
			while(!stack.isEmpty()) {
				int pnum = stack.peek();
				int ph = heights[pnum];	//	더 오른쪽에 있는 건물 높이
				
				if(ph > ch)	//	현재 건물보다 높은 건물이 나올때까지 제거하기
					break;
				
				stack.pop();
			}
			
			rightCnts[cnum] = stack.size();	//	stack에 남아있는 건물 개수가 현재 건물에서 볼 수 있는 건물 개수
			
			if(!stack.isEmpty())	//	볼 수 있는 건물이 있을 경우
				rightNum[cnum] = stack.peek();	//	그 중 가장 가까운 건물의 번호
			
			stack.push(cnum);
		}
		
		for(int cnum = 1; cnum <= N; cnum++) {
			sb.append(leftCnts[cnum] + rightCnts[cnum]);
			
			int left = leftNum[cnum];
			int right = rightNum[cnum];
			
			if(left == 0 && right == 0)
				sb.append("\n");
			else {
				sb.append(" ");
				
				if(left == 0)
					sb.append(right).append("\n");
				else if(right == 0)
					sb.append(left).append("\n");
				else {
					int leftDist = cnum - left;
					int rightDist = right - cnum;
					
					sb.append(leftDist > rightDist ? right : left).append("\n");
				}
			}
		}
			
		System.out.print(sb);
	} //	main-end
} //	Main-class-end
```