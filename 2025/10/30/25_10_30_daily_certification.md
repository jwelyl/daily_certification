# 25_10_30_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 31219 세계 일주

[31219번: 세계 일주](http://boj.ma/31219/t)

**선분교차**

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final Double NONE = Double.MAX_VALUE;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    국가 개수
    private static Pos[] posArr;    //    국가 위치
    private static boolean[] used;
    private static int[] order;
    
    private static double answer = NONE;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        posArr = new Pos[N];
        
        for(int nth = 0; nth < N; nth++) {
            st = new StringTokenizer(br.readLine());
            posArr[nth] = new Pos(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken()));
        }
        
        used = new boolean[N];
        order = new int[N];
        used[0] = true;
        order[0] = 0;
        backtracking(1, 0, new LineSegment[N]);
        	
        if(answer == NONE)
        	System.out.println(-1);
        else
        	System.out.println(answer);
    }    //    main-end
    
    private static void backtracking(int nth, double sum, LineSegment[] lsArr) {
        if(sum >= answer)
        	return;
    	
    	if(nth == N) {
            lsArr[nth - 1] = new LineSegment(posArr[order[nth - 1]], posArr[order[0]]);
            
            if(check(nth - 1, lsArr)) {
                sum += lsArr[nth - 1].length;
                answer = Math.min(answer, sum);
            }
            
            return;
        }
        
        for(int i = 1; i < N; i++) {
            if(!used[i]) {
                double nSum = sum;
                
                lsArr[nth - 1] = new LineSegment(posArr[order[nth - 1]], posArr[i]);
                
                if(check(nth - 1, lsArr)) {
                    nSum += lsArr[nth - 1].length;
                    used[i] = true;
                    order[nth] = i;
                    backtracking(nth + 1, nSum, lsArr);
                    used[i] = false;
                }
            }
        }
    }
    
    //    nth번째 선분이 그 이전 선분들과 교차하는지 체크
    //    교차하는 경우가 없을 경우 true
    private static boolean check(int nth, LineSegment[] lsArr) {
        LineSegment cur = lsArr[nth];
        
        for(int i = 0; i < nth; i++) {
            if(cur.isIntersect(lsArr[i]))
                return false;
        }
        
        return true;
    }
    
    private static class Pos {
        public long x;
        public long y;
        
        public Pos(int x, int y) {
            this.x = x;
            this.y = y;
        }
        
        public double length(Pos other) {
            return Math.sqrt((this.x - other.x) * (this.x - other.x) + (this.y - other.y) * (this.y - other.y));
        }
        
        @Override
        public boolean equals(Object obj) {
        	if(this == obj)
        		return true;
        	
        	if(this.getClass() == obj.getClass()) {
        		Pos other = (Pos)obj;
        		return this.x == other.x && this.y == other.y;
        	}
        	
        	return false;
        }
    }
    
    private static class LineSegment {
        public Pos pos1;
        public Pos pos2;
        public double length;
        
        public LineSegment(Pos pos1, Pos pos2) {
            this.pos1 = pos1;
            this.pos2 = pos2;
            this.length = pos1.length(pos2);
        }

        // 두 선분이 교차하는지 확인 (공유점 제외)
        public boolean isIntersect(LineSegment other) {
        	// 공유점 있는 경우 제외
            if (this.pos1.equals(other.pos1) || this.pos1.equals(other.pos2) ||
                this.pos2.equals(other.pos1) || this.pos2.equals(other.pos2)) {
                return false;
            }
        	
        	int ccw1 = ccw(this.pos1, this.pos2, other.pos1);
            int ccw2 = ccw(this.pos1, this.pos2, other.pos2);
            int ccw3 = ccw(other.pos1, other.pos2, this.pos1);
            int ccw4 = ccw(other.pos1, other.pos2, this.pos2);
            
            // 일반적인 교차
            if (ccw1 * ccw2 < 0 && ccw3 * ccw4 < 0) 
                return true;
        	
        	// 일직선 위(collinear)일 경우, 범위 겹침 확인
            if (ccw1 == 0 && onSegment(this.pos1, this.pos2, other.pos1)) 
                return true;
            if (ccw2 == 0 && onSegment(this.pos1, this.pos2, other.pos2)) 
                return true;
            if (ccw3 == 0 && onSegment(other.pos1, other.pos2, this.pos1)) 
                return true;
            if (ccw4 == 0 && onSegment(other.pos1, other.pos2, this.pos2)) 
                return true;
        	
            return false;
        }
        
        // ccw 계산
        private int ccw(Pos a, Pos b, Pos c) {
            long cross = (long)(b.x - a.x) * (c.y - a.y) - (long)(b.y - a.y) * (c.x - a.x);
            if (cross > 0) 
                return 1;
            if (cross < 0) 
                return -1;
            return 0;
        }

        private boolean onSegment(Pos pos1, Pos pos2, Pos pos3) {
            return Math.min(pos1.x, pos2.x) <= pos3.x && pos3.x <= Math.max(pos1.x, pos2.x)
                && Math.min(pos1.y, pos2.y) <= pos3.y && pos3.y <= Math.max(pos1.y, pos2.y);
        }
    }
}    //    Main-class-end
```

**어차피 교차할 경우 최솟값이 나올 수 없음**

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final Double NONE = Double.MAX_VALUE;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    국가 개수
    private static Pos[] posArr;    //    국가 위치
    private static boolean[] used;
    private static int[] order;
    
    private static double answer = NONE;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        posArr = new Pos[N];
        
        for(int nth = 0; nth < N; nth++) {
            st = new StringTokenizer(br.readLine());
            posArr[nth] = new Pos(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken()));
        }
        
        used = new boolean[N];
        order = new int[N];
        used[0] = true;
        order[0] = 0;
        backtracking(1, 0);
        	
        	System.out.println(answer);
    }    //    main-end
    
    private static void backtracking(int nth, double sum) {
        if(sum >= answer)
        	return;
    	
    	if(nth == N) {
            answer = Math.min(answer, sum + posArr[order[nth - 1]].length(posArr[order[0]]));
            return;
        }
        
        for(int i = 1; i < N; i++) {
            if(!used[i]) {
            	used[i] = true;
                order[nth] = i;
                backtracking(nth + 1, sum + posArr[order[nth - 1]].length(posArr[i]));
                used[i] = false;
            }
        }
    }
    
    private static class Pos {
        public long x;
        public long y;
        
        public Pos(int x, int y) {
            this.x = x;
            this.y = y;
        }
        
        public double length(Pos other) {
            return Math.sqrt((this.x - other.x) * (this.x - other.x) + (this.y - other.y) * (this.y - other.y));
        }
    }
}    //    Main-class-end
```