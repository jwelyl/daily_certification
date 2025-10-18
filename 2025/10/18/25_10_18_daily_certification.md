# 25_10_18_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1960 행렬만들기

[1960번: 행렬만들기](http://boj.ma/1960/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;            //    행렬 크기 N * N
    private static int[] rowCnt;     //	   rowCnt[row] : row행에 존재하는 1의 개수
    
    private static final PriorityQueue<Col> colPq = new PriorityQueue<>();
    
    private static boolean ok = true;
    private static char[][] answer;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        rowCnt = new int[N];
        answer = new char[N][N];
        
        st = new StringTokenizer(br.readLine());
        for(int y = 0; y < N; y++) {
           rowCnt[y] = Integer.parseInt(st.nextToken());
           Arrays.fill(answer[y], '0');
        }
        
        st = new StringTokenizer(br.readLine());
        for(int x = 0; x < N; x++) {
            int colCnt = Integer.parseInt(st.nextToken());
            if(colCnt > 0)
            	colPq.offer(new Col(x, colCnt));
        }

        for(int row = 0; row < N; row++) {
        	int cnt = rowCnt[row];					//	row 행에 필요한 1 개수
        	List<Col> tmp = new ArrayList<>();
        	
        	if(cnt == 0)	//	필요없을 경우
        		continue;
        	
        	if(colPq.size() < cnt) {	//	필요한 만큼 열이 존재하지 않을 경우
        		ok = false;
        		break;
        	}
        	
        	for(int i = 0; i < cnt; i++) {
        		Col col = colPq.poll();
        		answer[row][col.colPos] = '1';
        		col.cnt--;
        		
        		if(col.cnt > 0)
        			tmp.add(col);
        	}
        	
        	for(Col col : tmp)
        		colPq.offer(col);
        }
        
        if(!colPq.isEmpty())
        	ok = false;
        
        if(ok) {
        	sb.append("1\n");
        	for(int y = 0; y < N; y++)
        		sb.append(new String(answer[y])).append("\n");
        }
        else
        	sb.append("-1\n");
        
        System.out.print(sb);
    }    //    main-end
    
    private static class Col implements Comparable<Col> {
        public int colPos;         //	 열 index
        public int cnt;      	   //    해당 열에 필요한 1의 개수
        
        public Col(int colPos, int cnt) {
            this.colPos = colPos;
            this.cnt = cnt;
        }
        
        @Override
        public int compareTo(Col other) {
            return Integer.compare(other.cnt, this.cnt);
        }
    }
}    //    Main-class-end
```