# 26_04_11_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 10993 별 찍기 - 18

[10993번: 별 찍기 - 18](http://boj.ma/10993/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;

public class Main {
  private static final StringBuilder sb = new StringBuilder();

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    int n = Integer.parseInt(br.readLine());
    int h = (int)Math.pow(2, n) - 1;
    int w = 2 * h - 1;

    char[][] stars = new char[h][w];
    for(int i = 0; i < h; i++)
      Arrays.fill(stars[i], ' ');

    drawStars(n, 0, h - 1, 0, w - 1, stars, h, w);

    for(int i = 0; i < h; i++)
      sb.append(draw(new String(stars[i]))).append("\n");

    System.out.print(sb);
  } //  main-end

  private static String draw(String str) {
    int end = str.length() - 1;

    while(str.charAt(end) == ' ')
      end--;

    return str.substring(0, end + 1);
  }

  private static void drawStars(int n, int fy, int ty, int fx, int tx, char[][] stars, int h, int w) {
    int y = ty - fy + 1;		//	현재(n) 높이
    int x;                		//	현재(n) 너비
    int midy = (fy + ty) / 2;	//	현재(n) 세로 중점
    int midx = (fx + tx) / 2;	//	현재(n) 가로 중점

    if(n == 1) {
      stars[midy][midx] = '*';
      return;
    }

    y = (y - 1) / 2;		//	이전(n-1) 높이
    x = 2 * y - 1;			//	이전(n-1) 너비

    if(n % 2 == 0) {
      for(int i = fx; i <= tx; i++)
        stars[fy][i] = '*';

      for(int i = fy + 1; i <= ty; i++) {
        int diff = i - fy;
        stars[i][fx + diff] = '*';
        stars[i][tx - diff] = '*';
      }

      fy = fy + 1;
      ty = midy;
    }
    else {
      for(int i = fx; i <= tx; i++)
        stars[ty][i] = '*';

      for(int i = ty - 1; i >= fy; i--) {
        int diff = ty - i;
        stars[i][fx + diff] = '*';
        stars[i][tx - diff] = '*';
      }

      fy = midy;
      ty = ty - 1;
    }

    fx = midx - (x / 2);
    tx = midx + (x / 2);
    drawStars(n - 1, fy, ty, fx, tx, stars, h, w);
  }
} //  Main-class-end
```