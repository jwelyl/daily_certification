# 25_04_20_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 28303 자석

[28303번: 자석](http://boj.ma/28303/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));	
	private static StringTokenizer st;
	
	private static int N;
	private static int K;
    private static int[] arr;
    private static int[] energy;
    
    private static int min;
    private static int answer = Integer.MIN_VALUE;

    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        arr = new int[N];
        energy = new int[N];

        st = new StringTokenizer(br.readLine());
        for (int i = 0; i < N; i++)
            arr[i] = Integer.parseInt(st.nextToken());

        // 순방향
        for (int i = 0; i < N; i++)
            energy[i] = arr[i] - K * i;

        min = energy[0];
        for (int i = 1; i < N; i++) {
            answer = Math.max(energy[i] - min, answer);
            min = Math.min(min, energy[i]);
        }

        // 역방향
        for (int i = 0; i < N; i++)
            energy[i] = arr[N - i - 1] - K * i;

        min = energy[0];
        for (int i = 1; i < N; i++) {
            answer = Math.max(energy[i] - min, answer);
            min = Math.min(min, energy[i]);
        }

        System.out.println(answer);
    }	//	main-end
}	//	Main-class-end
```