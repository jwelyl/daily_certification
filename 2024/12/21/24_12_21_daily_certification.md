# 24_12_21_daily_certification

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