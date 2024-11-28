# 24_11_28_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ** 18783 Swapity Swapity Swap

[18783번: Swapity Swapity Swap](https://boj.ma/18783/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX = (int)Math.ceil((Math.log(1_000_000_000) / Math.log(2))) + 1;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    private static int K;
    private static int[][] function;	//	function[k][x] : 함수 x - f2^k -> f2^k(x) 의 f2^k(x)
    
    private static final List<Integer> bits = new ArrayList<>();
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        function = new int[MAX][N + 1];
        for(int x = 1; x <= N; x++)
        	function[0][x] = x;
        
        for(int m = 0; m < M; m++) {
        	st = new StringTokenizer(br.readLine());
        	int from = Integer.parseInt(st.nextToken());
        	int to = Integer.parseInt(st.nextToken());
        
        	int s = from;
        	int e = to;
        	
        	while(s < e) {
        		int tmp = function[0][s];
        		function[0][s] = function[0][e];
        		function[0][e] = tmp;
        		s++;
        		e--;
        	}
        }
        
        makeSparseMatrix();
        
        int idx = 0;
        while(K != 0) {
        	int r = K & 1;
        	
        	if(r == 1)
        		bits.add(idx);
        	
        	idx++;
        	K >>= 1;
        }
        
        for(int x = 1; x <= N; x++) {
        	int res = x;
        	
        	for(int bit : bits)
        		res = function[bit][res];
        	
        	sb.append(res).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static void makeSparseMatrix() {
    	for(int row = 1; row < MAX; row++) {
    		for(int x = 1; x <= N; x++)
    			function[row][x] = function[row - 1][function[row - 1][x]];
    	}
    }
}    //    Main-class-end
```

### **BOJ** 12348 분해합 2

[12348번: 분해합 2](https://boj.ma/12348/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static long N;
    
    public static void main(String[] args) throws IOException {
        N = Long.parseLong(br.readLine());
        
        for(long num = N - Long.toString(N).length() * 9; num <= N; num++) {
            if(constructor(num)) {
                System.out.println(num);
                return;
            }
        }
        
        System.out.println(0);
    }    //    main-end
    
    //    num이 N의 생성자일 경우 true
    private static boolean constructor(long num) {
        long sum = num;
        
        String numStr = Long.toString(num);
        for(int i = 0; i < numStr.length(); i++)
            sum += (numStr.charAt(i) - '0');
        
        return sum == N;
    }
}    //    Main-class-end
```

### **BOJ** 12908 텔레포트 3

[12908번: 텔레포트 3](https://boj.ma/12908/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.Arrays;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static final int[][] pos = new int[9][2];
    
    private static final long[][] dist = new long[9][9];
    
    public static void main(String[] args) throws IOException {
        for(int v = 1; v <= 8; v++)
            Arrays.fill(dist[v], Long.MAX_VALUE);
        
        st = new StringTokenizer(br.readLine());
        pos[1][0] = Integer.parseInt(st.nextToken());
        pos[1][1] = Integer.parseInt(st.nextToken());
        
        st = new StringTokenizer(br.readLine());
        pos[8][0] = Integer.parseInt(st.nextToken());
        pos[8][1] = Integer.parseInt(st.nextToken());
        
        for(int i = 2; i <= 6; i += 2) {
            st = new StringTokenizer(br.readLine());
            pos[i][0] = Integer.parseInt(st.nextToken());
            pos[i][1] = Integer.parseInt(st.nextToken());
            pos[i + 1][0] = Integer.parseInt(st.nextToken());
            pos[i + 1][1] = Integer.parseInt(st.nextToken());
            
            dist[i][i + 1] = 10;
            dist[i + 1][i] = 10;    //    텔레포트 시간
        }
        
        for(int v1 = 1; v1 <= 8; v1++) {
            int x1 = pos[v1][0];
            int y1 = pos[v1][1];
            
            for(int v2 = 1; v2 <= 8; v2++) {
                int x2 = pos[v2][0];
                int y2 = pos[v2][1];
                
                dist[v1][v2] = Math.min(dist[v1][v2], Math.abs(x2 - x1) + Math.abs(y2 - y1));
            }
        }
        
        floydWarshall();
        
        System.out.println(dist[1][8]);
    }    //    main-end
    
    private static void floydWarshall() {
        for(int k = 1; k <= 8; k++) {
            for(int i = 1; i <= 8; i++) {
                for(int j = 1; j <= 8; j++)
                    dist[i][j] = Math.min(dist[i][j], dist[i][k] + dist[k][j]);
            }
        }
    }
}    //    Main-class-end
```