# 26_01_25_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16900 이름 정하기

[16900번: 이름 정하기](http://boj.ma/16900/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());

    char[] string = st.nextToken().toCharArray();
    int k = Integer.parseInt(st.nextToken());
    int[] failure = makeFailure(string);

    System.out.println(string.length + (long) (k - 1) * (string.length - failure[string.length - 1]));
  } // main-end

  private static int[] makeFailure(char[] word) {
    int[] failure = new int[word.length];

    for (int i = 1; i < word.length; i++) {
      int j = failure[i - 1];

      while (j > 0 && word[i] != word[j])
        j = failure[j - 1];

      failure[i] = j + (word[i] == word[j] ? 1 : 0);
    }

    return failure;
  }
} // Main-class-end
```