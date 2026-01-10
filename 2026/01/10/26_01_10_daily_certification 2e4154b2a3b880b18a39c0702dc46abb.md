# 26_01_10_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 25341 인공 신경망

[25341번: 인공 신경망](http://boj.ma/25341/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    StringTokenizer st = new StringTokenizer(br.readLine());

    int n = Integer.parseInt(st.nextToken());    // 입력층 크기
    int m = Integer.parseInt(st.nextToken());    // 은닉층 인공 뉴런 개수
    int q = Integer.parseInt(st.nextToken());    // 입력 개수

    // 1 ~ m : 은닉층 인공 뉴런
    // m + 1 : 출력층 인공 뉴런
    AN[] anArr = new AN[m + 2];

    int c;
    int[] inputs;
    long[] weights;
    int bias;

    //  은닉층 인공 뉴런 정보 입력
    for(int i = 1; i <= m; i++) {
      st = new StringTokenizer(br.readLine());
      c = Integer.parseInt(st.nextToken());

      inputs = new int[c];
      weights = new long[c];

      for(int j = 0; j < c; j++)
        inputs[j] = Integer.parseInt(st.nextToken());
      for(int j = 0; j < c; j++)
        weights[j] = Integer.parseInt(st.nextToken());

      bias = Integer.parseInt(st.nextToken());
      anArr[i] = new AN(c, inputs, weights, bias);
    }

    //  출력층 인공 뉴런 정보 입력
    inputs = new int[m];
    for(int i = 0; i < m; i++)
      inputs[i] = i + 1;

    weights = new long[m];
    st = new StringTokenizer(br.readLine());
    for(int i = 0; i < m; i++)
      weights[i] = Integer.parseInt(st.nextToken());

    bias = Integer.parseInt(st.nextToken());
    anArr[m + 1] = new AN(m, inputs, weights, bias);

    //  계산 회수 감소 전처리
    long[] finalWeights = new long[n + 1];
    long finalBias = 0;

    for(int j = 1; j <= m; j++) {
      AN hidden = anArr[j];
      long outW = anArr[m + 1].WEIGHTS[j - 1];

      for(int i = 0; i < hidden.C; i++) {
        int nth = hidden.INPUTS[i];
        finalWeights[nth] += hidden.WEIGHTS[i] * outW;
      }

      finalBias += hidden.BIAS * outW;
    }

    finalBias += anArr[m + 1].BIAS;

    for(int i = 0; i < q; i++) {
      long[] inputLayers = new long[n + 1];
      st = new StringTokenizer(br.readLine());

      for(int j = 1; j <= n; j++)
        inputLayers[j] = Integer.parseInt(st.nextToken());

      long res = finalBias;
      for(int j = 1; j <= n; j++)
        res += inputLayers[j] * finalWeights[j];

      sb.append(res).append('\n');
    }

    System.out.print(sb);
  } //  main-end
} // Main-class-end

// 인공 뉴런
class AN {
  final int C;
  final int[] INPUTS;
  final long[] WEIGHTS;
  final int BIAS;

  public AN(int c, int[] inputs, long[] weights, int bias) {
    this.C = c;
    this.INPUTS = inputs;
    this.WEIGHTS = weights;
    this.BIAS = bias;
  }
}
```