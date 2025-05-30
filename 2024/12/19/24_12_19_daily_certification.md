# 24_12_19_daily_certification

```
[#354 koreii] 데일리인증 20241219
1. Java Exception
	- Method Override
	- Exception Wrapping
	- Custom Exception
2. 코딩 테스트 대비 알고리즘 학습
- Prefix Sum, imos (BOJ 25826 2차원 배열 다중 업데이트 단일 합), (프로그래머스 파괴되지 않은 건물)
- Two Pointer, Binary Search (BOJ 18114 블랙 프라이데이)
```

### **BOJ 25826** **2차원 배열 다중 업데이트 단일 합**

[25826번: 2차원 배열 다중 업데이트 단일 합](https://boj.ma/25826/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    배열 크기 N * N
    private static int M;    //    질의 개수
    private static long[][] arr;    //    원본 배열 -> 최종적으로 누적합 배열
    private static long[][] add;    //    질의 1로 (i1, j1), (i2, j2) 영역에 추가되는 것을 반영한 배열
    
    private static int query;
    private static int i1;
    private static int j1;
    private static int i2;
    private static int j2;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        arr = new long[N + 2][N + 2];
        add = new long[N + 2][N + 2];
        
        for(int i = 1; i <= N; i++) {
            st = new StringTokenizer(br.readLine());
            for(int j = 1; j <= N; j++)
                arr[i][j] = Integer.parseInt(st.nextToken());
        }
        
        for(int q = 0; q < M - 1; q++) {
            st = new StringTokenizer(br.readLine());
            query = Integer.parseInt(st.nextToken());
            
            if(query == 1) {
                i1 = Integer.parseInt(st.nextToken()) + 1;
                j1 = Integer.parseInt(st.nextToken()) + 1;
                i2 = Integer.parseInt(st.nextToken()) + 1;
                j2 = Integer.parseInt(st.nextToken()) + 1;
                int k = Integer.parseInt(st.nextToken());
                
                add[i1][j1] += k;
                add[i1][j2 + 1] -= k;
                add[i2 + 1][j1] -= k;
                add[i2 + 1][j2 + 1] += k;
            }
        }
        
        calc();
        st = new StringTokenizer(br.readLine());
        
        st.nextToken();
        i1 = Integer.parseInt(st.nextToken()) + 1;
        j1 = Integer.parseInt(st.nextToken()) + 1;
        i2 = Integer.parseInt(st.nextToken()) + 1;
        j2 = Integer.parseInt(st.nextToken()) + 1;
        
        System.out.println(partialSum(i1, j1, i2, j2));
    }    //    main-end
    
    private static void calc() {
        for(int i = 1; i <= N; i++) {
            for(int j = 1; j <= N; j++) {
                add[i][j] = add[i][j - 1] + add[i - 1][j] - add[i - 1][j - 1] + add[i][j];
                arr[i][j] += add[i][j];
                arr[i][j] = arr[i][j - 1] + arr[i - 1][j] - arr[i - 1][j - 1] + arr[i][j];
            }
        }
    }
    
    private static long partialSum(int i1, int j1, int i2, int j2) {
        return arr[i2][j2] - arr[i2][j1 - 1] - arr[i1 - 1][j2] + arr[i1 - 1][j1 - 1];
    }
}    //    Main-class-end
```

### 파괴되지 않은 건물

[](https://school.programmers.co.kr/learn/courses/30/lessons/92344)

```java
class Solution {
    public int solution(int[][] board, int[][] skill) {
        int answer = 0;
        
        int[][] add = new int[board.length + 2][board[0].length + 2];
        
        for(int i = 0; i < skill.length; i++) {
            int type = skill[i][0];
            int r1 = skill[i][1] + 1;
            int c1 = skill[i][2] + 1;
            int r2 = skill[i][3] + 1;
            int c2 = skill[i][4] + 1;
            int degree = skill[i][5];
            
            int d = type == 1 ? -degree : degree;
            
            add[r1][c1] += d;
            add[r1][c2 + 1] -= d;
            add[r2 + 1][c1] -= d;
            add[r2 + 1][c2 + 1] += d;
        }
        
        for(int r = 1; r <= board.length; r++) {
            for(int c = 1; c <= board[0].length; c++) {
                add[r][c] = add[r - 1][c] + add[r][c - 1] - add[r - 1][c - 1] + add[r][c];
                
                if(board[r - 1][c - 1] + add[r][c] >= 1)
                    answer++;
            }
        }
        
        return answer;
    }
}
```

### **BOJ 18114** 블랙 프라이데이

[18114번: 블랙 프라이데이](https://boj.ma/18114/t)

**Binary Search**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.Arrays;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    물건 개수
    private static int C;    //    필요 무게
    
    private static int[] weights;    //    물건 무게
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        C = Integer.parseInt(st.nextToken());
        weights = new int[N];
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++) {
            weights[i] = Integer.parseInt(st.nextToken());
            if(weights[i] == C) {    //    물건 한 개로 조합 만족 가능하면
                System.out.println(1);
                return;
            }
        }
        
        Arrays.sort(weights);    //    무게순 정렬
        
        for(int i1 = 0; i1 < N - 1; i1++) {
            for(int i2 = i1 + 1; i2 < N; i2++) {
                if(weights[i1] + weights[i2] == C //    물건 두 개로 조합 만족 가능하면
                   || binarySearch(i2 + 1, N - 1, C - weights[i1] - weights[i2]) != -1) {    //    물건 세 개로 조합 만족 가능하면
                    System.out.println(1);
                    return;
                }
            }
        }
        
        System.out.println(0);    //    만족하는 조합이 없는 경우
    }    //    main-end
    
    private static int binarySearch(int start, int end, int target) {
        int res = -1;
        
        while(start <= end) {
            int mid = (start + end) / 2;
            
            if(weights[mid] == target) {
                res = mid;
                break;
            }
            
            if(weights[mid] < target)
                start = mid + 1;
            else
                end = mid - 1;
        }
        
        return res;
    }
}    //    Main-class-end
```

**Two Pointer + Binary Search**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.Arrays;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    물건 개수
    private static int C;    //    필요 무게
    
    private static int[] weights;    //    물건 무게
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        C = Integer.parseInt(st.nextToken());
        weights = new int[N];
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++) {
            weights[i] = Integer.parseInt(st.nextToken());
            if(weights[i] == C) {    //    물건 한 개로 조합 만족 가능하면
                System.out.println(1);
                return;
            }
        }
        
        Arrays.sort(weights);    //    무게순 정렬
        
        int start = 0;
        int end = N - 1;	//	two pointer
        
        while(start < end) {
        	if(weights[start] + weights[end] == C) {	//	물건 두 개로 조합 만족 가능하면
        		System.out.println(1);
                return;
        	}
        	else if(weights[start] + weights[end] > C)	//	물건 두 개 무게 합이 이미 초과할 경우
	        	end--;	//	두 무게 합 줄여보기
        	else {	//	물건 두 개만으론부족한 경우
        		int diff = C - weights[start] - weights[end];
        		int idx = -1;
        		
        		if(diff < weights[start])
        			idx = binarySearch(0, start - 1, diff);
        		else if(weights[start] < diff && diff < weights[end])
        			idx = binarySearch(start + 1, end - 1, diff);
        		else if(weights[end] < diff)
        			idx = binarySearch(end + 1, N - 1, diff);
        		
        		if(idx != -1) {
        			System.out.println(1);
                    return;
        		}
        		
        		start++;
        	}
        }
        
        System.out.println(0);    //    만족하는 조합이 없는 경우
    }    //    main-end
    
    private static int binarySearch(int start, int end, int target) {
        int res = -1;
        
        while(start <= end) {
            int mid = (start + end) / 2;
            
            if(weights[mid] == target) {
                res = mid;
                break;
            }
            
            if(weights[mid] < target)
                start = mid + 1;
            else
                end = mid - 1;
        }
        
        return res;
    }
}    //    Main-class-end
```