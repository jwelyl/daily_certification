# 24_12_20_daily_certification

### **BOJ** 25978 **2차원 배열 다중 업데이트 다중 합**

[25978번: 2차원 배열 다중 업데이트 다중 합](https://boj.ma/25978/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;    //    배열 크기 N * N
    private static int M;    //    질의 개수
    private static long[][] arr;    //    원본 배열 -> 최종적으로 누적합 배열
    private static long[][] add;    //    질의 1로 (i1, j1), (i2, j2) 영역에 추가되는 것을 반영한 배열
    
    private static boolean isEnd = false;
    
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
        
        for(int q = 0; q < M; q++) {
            st = new StringTokenizer(br.readLine());
            int query = Integer.parseInt(st.nextToken());
            
            if(query == 1) {
                int i1 = Integer.parseInt(st.nextToken()) + 1;
                int j1 = Integer.parseInt(st.nextToken()) + 1;
                int i2 = Integer.parseInt(st.nextToken()) + 1;
                int j2 = Integer.parseInt(st.nextToken()) + 1;
                int k = Integer.parseInt(st.nextToken());
                
                add[i1][j1] += k;
                add[i1][j2 + 1] -= k;
                add[i2 + 1][j1] -= k;
                add[i2 + 1][j2 + 1] += k;
            }
            else {
                if(!isEnd) {   //    질의 1이 이제 끝난 경우, 더 이상 갱신될 일 없음, 누적합 계산
                    isEnd = true;
                    calc();
                }
                
                int i1 = Integer.parseInt(st.nextToken()) + 1;
                int j1 = Integer.parseInt(st.nextToken()) + 1;
                int i2 = Integer.parseInt(st.nextToken()) + 1;
                int j2 = Integer.parseInt(st.nextToken()) + 1;
                
                sb.append(partialSum(i1, j1, i2, j2)).append("\n");        
            }
        }
        
        System.out.print(sb);
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

### **BOJ** 5536 물고기의 서식 범위

[5536번: 물고기의 서식 범위](https://boj.ma/5536/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.HashMap;
import java.util.HashSet;
import java.util.List;
import java.util.Map;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int K;
	
	private static Cuboid[] cuboids;
	private static final Set<Integer> xSet = new HashSet<>();
	private static final Set<Integer> ySet = new HashSet<>();
	private static final Set<Integer> zSet = new HashSet<>();
	
	private static List<Integer> xList;
	private static List<Integer> yList;
	private static List<Integer> zList;
	
	private static int xSize;
	private static int ySize;
	private static int zSize;
	
	private static Map<Integer, Integer> xMap;
	private static Map<Integer, Integer> yMap;
	private static Map<Integer, Integer> zMap;
	
	private static int[][][] prefixSum;
	private static long answer = 0;
	
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());

        cuboids = new Cuboid[N];

        for (int i = 0; i < N; i++) {
        	st = new StringTokenizer(br.readLine());
            int x1 = Integer.parseInt(st.nextToken());
            int y1 = Integer.parseInt(st.nextToken());
            int z1 = Integer.parseInt(st.nextToken());
            int x2 = Integer.parseInt(st.nextToken());
            int y2 = Integer.parseInt(st.nextToken());
            int z2 = Integer.parseInt(st.nextToken());
            
            cuboids[i] = new Cuboid(x1, y1, z1, x2, y2, z2);
            xSet.add(x1);
            xSet.add(x2);
            ySet.add(y1);
            ySet.add(y2);
            zSet.add(z1);
            zSet.add(z2);
        }

        // 좌표 압축
        xList = new ArrayList<>(xSet);
        yList = new ArrayList<>(ySet);
        zList = new ArrayList<>(zSet);
        Collections.sort(xList);
        Collections.sort(yList);
        Collections.sort(zList);

        xMap = compress(xList);
        yMap = compress(yList);
        zMap = compress(zList);

        xSize = xList.size();
        ySize = yList.size();
        zSize = zList.size();

        prefixSum = new int[xSize][ySize][zSize];

        // imos 알고리즘 적용
        for (Cuboid cuboid : cuboids) {
            int x1 = xMap.get(cuboid.x1);
            int y1 = yMap.get(cuboid.y1);
            int z1 = zMap.get(cuboid.z1);
            int x2 = xMap.get(cuboid.x2);
            int y2 = yMap.get(cuboid.y2);
            int z2 = zMap.get(cuboid.z2);

            prefixSum[x1][y1][z1]++;
            prefixSum[x2][y1][z1]--;
            prefixSum[x1][y2][z1]--;
            prefixSum[x1][y1][z2]--;
            prefixSum[x2][y2][z1]++;
            prefixSum[x2][y1][z2]++;
            prefixSum[x1][y2][z2]++;
            prefixSum[x2][y2][z2]--;
        }

        calc();

        System.out.println(answer);
    }	//	main-end
    
    private static void calc() {
    	// 3차원 누적합
        for (int x = 1; x < xSize; x++) {
            for (int y = 0; y < ySize; y++) {
                for (int z = 0; z < zSize; z++)
                    prefixSum[x][y][z] += prefixSum[x - 1][y][z];
            }
        }

        for (int x = 0; x < xSize; x++) {
            for (int y = 1; y < ySize; y++) {
                for (int z = 0; z < zSize; z++)
                    prefixSum[x][y][z] += prefixSum[x][y - 1][z];
            }
        }

        for (int x = 0; x < xSize; x++) {
            for (int y = 0; y < ySize; y++) {
                for (int z = 1; z < zSize; z++)
                    prefixSum[x][y][z] += prefixSum[x][y][z - 1];
            }
        }

        // K종 이상 서식하는 영역 부피 계산
        for (int x = 0; x < xSize - 1; x++) {
            for (int y = 0; y < ySize - 1; y++) {
                for (int z = 0; z < zSize - 1; z++) {
                    if (prefixSum[x][y][z] >= K) {
                        long dx = xList.get(x + 1) - xList.get(x);
                        long dy = yList.get(y + 1) - yList.get(y);
                        long dz = zList.get(z + 1) - zList.get(z);
                        answer += dx * dy * dz;
                    }
                }
            }
        }
    }

    private static Map<Integer, Integer> compress(List<Integer> list) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < list.size(); i++) {
            map.put(list.get(i), i);
        }
        return map;
    }
    
    private static class Cuboid {
        public int x1; 
        public int y1;
        public int z1;
        public int x2;
        public int y2;
        public int z2;

        public Cuboid(int x1, int y1, int z1, int x2, int y2, int z2) {
            this.x1 = x1;
            this.y1 = y1;
            this.z1 = z1;
            this.x2 = x2;
            this.y2 = y2;
            this.z2 = z2;
        }
    }
}	//	Main-class-end
```