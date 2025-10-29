# 25_10_29_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16821 **Low Range-Sum Matrix**

[16821번: Low Range-Sum Matrix](http://boj.ma/16821/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    private static int K;
    private static int S;
    
    private static int[][] arr;
    private static boolean[][] used;
    
    private static final List<Integer> rowList = new ArrayList<>();
    private static final List<Integer> colList = new ArrayList<>();
    private static final List<int[]> posList = new ArrayList<>();
    private static boolean find = false;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        S = Integer.parseInt(st.nextToken());
        
        arr = new int[N][M];
        used = new boolean[N][M];
        for(int y = 0; y < N; y++) {
        	st = new StringTokenizer(br.readLine());
        	for(int x = 0; x < M; x++)
        		arr[y][x] = Integer.parseInt(st.nextToken());
        }
      
        for(int y = 0; y < N; y++) {
        	if(exceed(y, true))		//	y행이 문제가 될 경우
        		rowList.add(y);
        }
        
        for(int x = 0; x < M; x++) {
        	if(exceed(x, false))	//	x열이 문제가 될 경우
        		colList.add(x);
        }
        
        if(rowList.isEmpty() && colList.isEmpty()) {	//	문제되는 행/열이 전혀 없을 경우
        	System.out.println("Yes");
        	return;
        }
        
        //	문제 되는 행에서 값이 양수인 칸의 좌표만 가져오기
        for(int y : rowList) {
        	for(int x = 0; x < M; x++) {
        		if(arr[y][x] > 0 && !used[y][x]) {
        			used[y][x] = true;
        			posList.add(new int[] {y, x});
        		}
        	}
        }
        //  문제 되는 열에서 값이 양수인 칸의 좌표만 가져오기
        for(int x: colList) {
        	for(int y = 0; y < N; y++) {
        		if(arr[y][x] > 0 && !used[y][x]) {
        			used[y][x] = true;
        			posList.add(new int[] {y, x});
        		}
        	}
        }
        
        K = Math.min(K, posList.size());
        
        backtracking(0, 0);
        
        System.out.println(find ? "Yes" : "No");
    }    //    main-end
    
    private static void backtracking(int nth, int start) {
    	if(find || nth == K) 
    		return;
    
    	for(int i = start; i < posList.size(); i++) {
    		int[] pos = posList.get(i);
    		int y = pos[0];
    		int x = pos[1];
    		
    		arr[y][x] *= -1;	//	(y, x)칸 뒤집기
    		
    		if(check())	{ //	(y, x)칸을 뒤집음으로서 조건 만족할 경우
    			find = true;
    			return;
    		}
    		
    		backtracking(nth + 1, i + 1);
    		
    		arr[y][x] *= -1;	//	원상복구
    	}
    }
    
    private static boolean check() {
    	//	문제되는 행만 체크
    	for(int y : rowList)
    		if(exceed(y, true))
    			return false;
    	//	문제되는 열만 체크
    	for(int x : colList)
    		if(exceed(x, false))
    			return false;
    	
    	return true;
    }
    
    //	row = true : idx행의 부분합이 S를 초과할 경우 true
    //	row = false : idx열의 부분합이 S를 초과할 경우 true
    private static boolean exceed(int idx, boolean row) {
    	int prefixSum = 0;
    	int minPrefixSum = 0;
    	
    	if(row) {
    		for(int i = 0; i < M; i++) {
    			prefixSum += arr[idx][i];
    			
    			if(prefixSum - minPrefixSum > S)	//	idx행의 0열부터 i열까지 가능한 최대 부분합이 S보다 클 경우
    				return true;
    			
    			minPrefixSum = Math.min(minPrefixSum, prefixSum);
    		}
    	}
    	else {
    		for(int i = 0; i < N; i++) {
    			prefixSum += arr[i][idx];
    			
    			if(prefixSum - minPrefixSum > S)	//	idx열의 0행부터 i행까지 가능한 최대 부분합이 S보다 클 경우
    				return true;
    			
    			minPrefixSum = Math.min(minPrefixSum, prefixSum);
    		}
    	}
    	
    	return false;
    }
}    //    Main-class-end
```