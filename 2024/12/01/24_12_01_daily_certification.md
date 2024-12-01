# 24_12_01_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ** 7682 틱택토

[7682번: 틱택토](https://boj.ma/7682/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    
    private static final char[][] tictactoe = new char[3][3];
    
    public static void main(String[] args) throws IOException {
    	while(true) {
    		String input = br.readLine();
    		
    		if(input.equals("end")) {
    			System.out.print(sb);
    			break;
    		}
    		
    		int xCnt = 0;
    		int oCnt = 0;
    		boolean xWin = false;
    		boolean oWin = false;
    		boolean ok = true;
    		
    		for(int i = 0; i < 3; i++) {
    			for(int j = 0; j < 3; j++) {
    				char ch = input.charAt(3 * i + j);
    				tictactoe[i][j] = ch;
    			
    				if(ch == 'X')
    					xCnt++;
    				else if(ch == 'O')
    					oCnt++;
    			}
    		}
    		
			if(tictactoe[0][0] == 'X' && tictactoe[1][1] == 'X' && tictactoe[2][2] == 'X')
				xWin = true;
			if(tictactoe[0][2] == 'X' && tictactoe[1][1] == 'X' && tictactoe[2][0] == 'X')
				xWin = true;
			if(tictactoe[0][0] == 'X' && tictactoe[0][1] == 'X' && tictactoe[0][2] == 'X')
				xWin = true;
			if(tictactoe[1][0] == 'X' && tictactoe[1][1] == 'X' && tictactoe[1][2] == 'X')
				xWin = true;
			if(tictactoe[2][0] == 'X' && tictactoe[2][1] == 'X' && tictactoe[2][2] == 'X')
				xWin = true;
			if(tictactoe[0][0] == 'X' && tictactoe[1][0] == 'X' && tictactoe[2][0] == 'X')
				xWin = true;
			if(tictactoe[0][1] == 'X' && tictactoe[1][1] == 'X' && tictactoe[2][1] == 'X')
				xWin = true;
			if(tictactoe[0][2] == 'X' && tictactoe[1][2] == 'X' && tictactoe[2][2] == 'X')
				xWin = true;

			if(tictactoe[0][0] == 'O' && tictactoe[1][1] == 'O' && tictactoe[2][2] == 'O')
				oWin = true;
			if(tictactoe[0][2] == 'O' && tictactoe[1][1] == 'O' && tictactoe[2][0] == 'O')
				oWin = true;
			if(tictactoe[0][0] == 'O' && tictactoe[0][1] == 'O' && tictactoe[0][2] == 'O')
				oWin = true;
			if(tictactoe[1][0] == 'O' && tictactoe[1][1] == 'O' && tictactoe[1][2] == 'O')
				oWin = true;
			if(tictactoe[2][0] == 'O' && tictactoe[2][1] == 'O' && tictactoe[2][2] == 'O')
				oWin = true;
			if(tictactoe[0][0] == 'O' && tictactoe[1][0] == 'O' && tictactoe[2][0] == 'O')
				oWin = true;
			if(tictactoe[0][1] == 'O' && tictactoe[1][1] == 'O' && tictactoe[2][1] == 'O')
				oWin = true;
			if(tictactoe[0][2] == 'O' && tictactoe[1][2] == 'O' && tictactoe[2][2] == 'O')
				oWin = true;
			
			if(xWin && oWin)	//	둘 다 승리할 수는 없음
				ok = false;
			else if(xWin) {	//	X가 이긴 경우
				if(xCnt != oCnt + 1)	//	X가 1개 더 놓은 상태여야 함
					ok = false;
			}
			else if(oWin) {	//	O가 이긴 경우
				if(oCnt != xCnt)	//	X와 O가 같은 개수여야 함
					ok = false;
			}
			else {	//	비긴 경우
				if(xCnt != oCnt + 1)	//	X가 1개 더 놓은 상태여야 함
					ok = false;
				if(xCnt + oCnt != 9)	//	최종상태가 아님
					ok = false;
			}
			
			sb.append(ok ? "valid" : "invalid").append("\n");
    	}
    }    //    main-end
}    //    Main-class-end
```

### LeetCode Search in Rotated Sorted Array

[Search in Rotated Sorted Array - LeetCode](https://leetcode.com/problems/search-in-rotated-sorted-array/description/)

```java
class Solution {
    public int search(int[] nums, int target) {
        int end = binarySearch(nums);

        int left = binarySearch(nums, 0, end, target);
        
        if(left != -1)  //  [0, end]에 target이 존재할 경우
            return left;

        return binarySearch(nums, end + 1, nums.length - 1, target);    //  end 이후에서 찾아보기
    }

    //  [start, end]에 속하는 index 중 target이 존재하면 index 반환, 없으면 -1 반환
    private int binarySearch(int[] nums, int start, int end, int target) {
        while(start <= end) {
            int mid = (start + end) / 2;

            if(nums[mid] == target) //  찾았을 경우
                return mid;

            if(nums[mid] > target)  //  더 클 경우
                end = mid - 1;  //  더 작은 범위에서 찾아야 함
            else
                start = mid + 1;
        }

        return -1;  //  찾지 못했을 경우
    }

    //  index 0부터 시작해서 증가하는 가장 마지막 index
    private int binarySearch(int[] nums) {
        int start = 0;
        int end = nums.length - 1;
        int res = 0;
        
        while(start <= end) {
            int mid = (start + end) / 2;

            if(nums[mid] >= nums[0]) {   //  mid까지는 계속 증가할 경우
                res = mid;  //  일단 mid 저장
                start = mid + 1;    //  더 큰 mid 찾아보기
            }
            else
                end = mid - 1;
        }

        return res;
    }
}
```