# 24_03_27_daily_certification

# Problem Solving (Algorithm & SQL)

**Programmers 쿼드압축 후 개수 세기**

[https://school.programmers.co.kr/learn/courses/30/lessons/68936](https://school.programmers.co.kr/learn/courses/30/lessons/68936)

[](https://school.programmers.co.kr/learn/courses/30/lessons/68936)

**코드**

```java
class Solution {
    public int[] solution(int[][] arr) {
        int n = arr.length;
        
        return quadTree(arr, 0, 0, n - 1, n - 1);
    }
    
    private int[] quadTree(int[][] arr, int sy, int sx, int ey, int ex) {     
        int[] ret = {0, 0};
        
        if(sy == ey && sx == ex) {  //  크기 1인 정사각형일 경우
            if(arr[sy][sx] == 0)
                ret[0] = 1;
            else
                ret[1] = 1;
            
            return ret;
        }
        
        int midY = (sy + ey) / 2;
        int midX = (sx + ex) / 2;
        
        int[] startY = {sy, sy, midY + 1, midY + 1};
        int[] startX = {sx, midX + 1, sx, midX + 1};
        int[] endY = {midY, midY, ey, ey};
        int[] endX = {midX, ex, midX, ex};
        
        for(int i = 0; i < 4; i++) {
            int[] partialQuadTree = quadTree(arr, startY[i], startX[i], endY[i], endX[i]);
            
            if(partialQuadTree[0] == 0)
                partialQuadTree[1] = 1;
            else if(partialQuadTree[1] == 0)
                partialQuadTree[0] = 1;
            
            ret[0] += partialQuadTree[0];
            ret[1] += partialQuadTree[1];
        }

        
        if(ret[0] == 0)
            ret[1] = 1;
        else if(ret[1] == 0)
            ret[0] = 1;
        
        return ret;
    }
}
```

**Programmers 테이블 해시 함수**

[https://school.programmers.co.kr/learn/courses/30/lessons/147354](https://school.programmers.co.kr/learn/courses/30/lessons/147354)

[](https://school.programmers.co.kr/learn/courses/30/lessons/147354)

**코드 (Bruteforcing)**

```java
import java.util.*;

class Solution {
    private int numOfRows;  //  튜플 개수
    private int numOfCols;  //  컬럼 개수
    
    public int solution(int[][] data, int col, int row_begin, int row_end) {
        int answer = 0;
        
        numOfRows = data.length;
        numOfCols = data[0].length;
        
        int[] s = new int[numOfRows];
        
        Arrays.sort(data, new Comparator<int[]>() {
            @Override
            public int compare(int[] tuple1, int[] tuple2) {
                if(tuple1[col - 1] == tuple2[col - 1])  //  col 컬럼 값이 같을 경우
                    return Integer.compare(tuple2[0], tuple1[0]);   //  pk로 내림차순 정렬
                
                return Integer.compare(tuple1[col - 1], tuple2[col - 1]);   //  col 컬럼 값으로 오름차순 정렬
            }
        });
        
        for(int r = row_begin - 1; r <= row_end - 1; r++) {
            int[] tuple = data[r];  //  튜플
            
            for(int c = 0; c < numOfCols; c++)
                s[r] += (tuple[c] % (r + 1));

            if(r == row_begin - 1)
                answer = s[r];
            else
                answer = answer ^ s[r];
        }
        
        return answer;
    }
}
```