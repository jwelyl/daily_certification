# 24_12_28_daily_certification

### **BOJ** 13168 ****내일로 여행

[13168번: 내일로 여행](https://boj.ma/13168/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.*;

public class Main {
    private static final double INF = Double.MAX_VALUE;

    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;

    private static int N;
    private static int R;
    private static int T;

    private static double[][][] dp;
    private static final Map<String, Integer> map = new HashMap<>();
    private static final List<Integer> list = new ArrayList<>();

    private static int M;

    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        R = Integer.parseInt(st.nextToken());

        st = new StringTokenizer(br.readLine());
        int num = 1;

        while (st.hasMoreTokens()) {
            String city = st.nextToken();
            if (!map.containsKey(city)) {
                map.put(city, num++);
            }
        }

        T = Integer.parseInt(br.readLine());

        st = new StringTokenizer(br.readLine());
        while (st.hasMoreTokens()) {
            String city = st.nextToken();
            list.add(map.get(city));
        }

        M = Integer.parseInt(br.readLine());

        dp = new double[2][N + 1][N + 1];
        for (int k = 0; k < 2; k++) {
            for (int i = 1; i <= N; i++) {
                Arrays.fill(dp[k][i], INF);
                dp[k][i][i] = 0;
            }
        }

        for (int e = 0; e < M; e++) {
            st = new StringTokenizer(br.readLine());

            String type = st.nextToken();
            int v1 = map.get(st.nextToken());
            int v2 = map.get(st.nextToken());
            int cost = Integer.parseInt(st.nextToken());

            dp[0][v1][v2] = Math.min(dp[0][v1][v2], cost);
            dp[0][v2][v1] = Math.min(dp[0][v2][v1], cost);

            if (type.equals("Mugunghwa") || type.equals("ITX-Saemaeul") || type.equals("ITX-Cheongchun")) {
                dp[1][v1][v2] = Math.min(dp[1][v1][v2], 0);
                dp[1][v2][v1] = Math.min(dp[1][v2][v1], 0);
            } else if (type.equals("S-Train") || type.equals("V-Train")) {
                dp[1][v1][v2] = Math.min(dp[1][v1][v2], cost / 2.0);
                dp[1][v2][v1] = Math.min(dp[1][v2][v1], cost / 2.0);
            } else {
                dp[1][v1][v2] = Math.min(dp[1][v1][v2], cost);
                dp[1][v2][v1] = Math.min(dp[1][v2][v1], cost);
            }
        }

        floydWarshall();

        double sum0 = 0;
        double sum1 = R;

        for (int i = 0; i < T - 1; i++) {
            int from = list.get(i);
            int to = list.get(i + 1);

            sum0 += dp[0][from][to];
            sum1 += dp[1][from][to];
        }

        System.out.println(sum0 > sum1 ? "Yes" : "No");
    }	//	main-end

    private static void floydWarshall() {
        for (int k = 1; k <= N; k++) {
            for (int i = 1; i <= N; i++) {
                for (int j = 1; j <= N; j++) {
                    dp[0][i][j] = Math.min(dp[0][i][j], dp[0][i][k] + dp[0][k][j]);
                    dp[1][i][j] = Math.min(dp[1][i][j], dp[1][i][k] + dp[1][k][j]);
                }
            }
        }
    }
}	//	Main-class-end
```