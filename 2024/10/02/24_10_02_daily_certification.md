# 24_10_02_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 22945 팀 빌딩

[](https://www.acmicpc.net/problem/22945)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int[] abilities;
    
    private static long ans = 0L;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        abilities = new int[N];
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++)
            abilities[i] = Integer.parseInt(st.nextToken());
        
        int start = 0;
        int end = N - 1;
        
        while(start < end) {
            ans = Math.max(ans, (long)Math.min(abilities[start], abilities[end]) * (end - start - 1));
            
            if(abilities[start] <= abilities[end])    //    start의 값이 더 작거나 같다면
                start++;
            else
                end--;
        }
        
        System.out.println(ans);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 10800 컬러볼

[](https://www.acmicpc.net/problem/10800)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
    private static final int MAX = 200_001;    //    볼 색 범위 (1 ~ 200,000)
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;    //    볼 개수
    
    private static final List<Ball> balls = new ArrayList<>();
    
    private static int ballIdx = 0;
    private static int sum = 0;
    private static final int[] colors = new int[MAX];    //    colors[i] : [0, ballIdx]까지 볼들 중에서 색이 i인 볼들의 크기 합
    
    private static int[] answers;    //    answers[i] : i번째 볼이 잡은 볼 크기 합
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        answers = new int[N];
        
        for(int b = 0; b < N; b++) {
            st = new StringTokenizer(br.readLine());
            int color = Integer.parseInt(st.nextToken());
            int size = Integer.parseInt(st.nextToken());
            
            balls.add(new Ball(b, color, size));
        }
        
        Collections.sort(balls);    //    크기순 정렬
        
        for(int i = 0; i < N; i++) {
            Ball cBall = balls.get(i);    //    현재 볼
            int cnum = cBall.num;
            int ccolor = cBall.color;
            int csize = cBall.size;
            
            while(balls.get(ballIdx).size < csize) {    //    현재 볼이 잡을 가능성이 있는 볼들에 대해
                sum += balls.get(ballIdx).size;    //    현재 볼들보다 크기 작은 볼 크기 추가
                colors[balls.get(ballIdx).color] += balls.get(ballIdx).size;    //    색깔에 따른 크기 추가
                ballIdx++;
            }
            
            //    현재 볼 색깔과 같은 볼들의 크기 합 배제
            answers[cnum] = sum - colors[ccolor];
        }
        
        for(int i = 0; i < N; i++)
            sb.append(answers[i]).append("\n");
        
        System.out.print(sb);
    }    //    main-end
    
    private static class Ball implements Comparable<Ball> {
        public int num;        //    볼 번호
        public int color;      //    볼 색깔
        public int size;       //    볼 크기
     
        public Ball(int num, int color, int size) {
            this.num = num;
            this.color = color;
            this.size = size;
        }
        
        @Override
        public int compareTo(Ball other) {
            return Integer.compare(this.size, other.size);    //    볼 크기 순으로 오름차순 정렬
        }
    }
}    //    Main-class-end
```

### BOJ 2539 모자이크

[](https://www.acmicpc.net/problem/2539)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int R;    //    행 개수 (1 ~ R)
    private static int C;    //    열 개수 (1 ~ C)
    
    private static int N;    //    색종이 수
    private static int M;    //    잘못 칠해진 칸 개수
    
    private static int[] cols;        //    잘못 칠해진 칸의 열 좌표
    private static int maxRow = 0;    //    잘못 칠해진 칸 중 행 좌표가 가장 큰 칸의 행 좌표
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        R = Integer.parseInt(st.nextToken());
        C = Integer.parseInt(st.nextToken());
        
        N = Integer.parseInt(br.readLine());
        M = Integer.parseInt(br.readLine());
        
        cols = new int[M];
        
        for(int i = 0; i < M; i++) {
            st = new StringTokenizer(br.readLine());
            maxRow = Math.max(maxRow, Integer.parseInt(st.nextToken()));
            cols[i] = Integer.parseInt(st.nextToken());
        }
        
        Arrays.sort(cols);    //    열 좌표를 오름차순으로 정렬
        
        System.out.println(parametricSearch());
    }    //    main-end
    
    private static int parametricSearch() {
        int start = maxRow;            //    모든 칸을 덮을 수 있기 위한 최소 크기
        int end = Math.max(R, C);
        int ret = end;
        
        while(start <= end) {
            int mid = (start + end) / 2;    //    mid * mid 크기 색종이
            
            if(canMosaic(mid)) {    //    mid * mid 크기 색종이 N장으로 덮을 수 있으면
                ret = mid;
                end = mid - 1;	//	색종이 크기 더 줄여보기
            }
            else    //    mid * mid 크기 색종이 N장으로 부족하면
                start = mid + 1;	//	색종이 크기 더 키워보기
        }
        
        return ret;
    }
    
    private static boolean canMosaic(int size) {
        int cnt = 1;    //    M개 칸을 모두 덮기 위해 필요한 색종이 수
        
        int start = 0;    //    열이 start인 칸부터 덮기 시작
        int idx = 1;
        
        while(cnt <= N) {
            while(idx < M && cols[idx] - cols[start] + 1 <= size)    //    start부터 idx까지 덮을 수 있을 경우
                idx++;
            
            if(idx == M)    //    모두 덮었을 경우
            	return true;
            
            cnt++;    //    필요한 종이 한 장 증가
            start = idx;
            idx++;
        }

        return false;
    }
}    //    Main-class-end
```