# 26_04_17_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 5620 **가장 가까운 두 점의 거리**

[](http://boj.ma/5620/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
  private static final int INF = Integer.MAX_VALUE;

  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private static StringTokenizer st;

  private static int N; // 점의 개수
  private static final List<Point> pointList = new ArrayList<>(); // 점 좌표 저장 리스트

  public static void main(String[] args) throws IOException {
    N = Integer.parseInt(br.readLine());

    //	좌표 입력 받음
    for(int i = 0; i < N; i++) {
      st = new StringTokenizer(br.readLine());
      pointList.add(new Point(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken())));
    }

    Collections.sort(pointList, sortByX);

    System.out.println(closestPair(0, N - 1));
  }   //  main-end

  // 두 점 사이의 거리의 제곱
  static int dist(Point p1, Point p2) {
    return (p2.x - p1.x) * (p2.x - p1.x) + (p2.y - p1.y) * (p2.y - p1.y);
  }

  // 점 좌표 class
  private static class Point {
    public int x;
    public int y;

    public Point(int x, int y) {
      this.x = x;
      this.y = y;
    }
  }

  // x 좌표를 기준으로 정렬
  private static Comparator<Point> sortByX = new Comparator<Point>() {
    @Override
    public int compare(Point p1, Point p2) {
      return Integer.compare(p1.x, p2.x);
    }
  };

  //	y 좌표를 기준으로 정렬
  private static Comparator<Point> sortByY = new Comparator<Point>() {
    @Override
    public int compare(Point p1, Point p2) {
      return Integer.compare(p1.y, p2.y);
    }
  };

  //	[start, end] 범위의 점들 중 2개를 뽑아 거리를 계산 후 그 중 최소 거리를 반환함
  private static int calcBruteForce(int start, int end) {
    int ret = INF;

    for(int i = start; i < end; i++) {
      for(int j = i + 1; j <= end; j++) {
        int tmp = dist(pointList.get(i), pointList.get(j));

        ret = Math.min(ret, tmp);
      }
    }

    return ret;
  }

  //	[start, end]에 존재하는 점들 중 가장 가까운 두 쌍의 거리를 반환
  private static int closestPair(int start, int end) {
    //	점의 개수가 3개 이하이면 brute-force로 거리 구함
    if(end - start < 3) {
      return calcBruteForce(start, end);
    }

    //	가운데 index
    int mid = (start + end) / 2;

    //	[start, mid]에 존재하는 점들 중 가장 가까운 두 쌍의 거리를 반환
    int minDistLeft = closestPair(start, mid);
    //	[mid + 1, end]에 존재하는 점들 중 가장 가까운 두 쌍의 거리를 반환
    int minDistRight = closestPair(mid + 1, end);

    int minDist = Math.min(minDistLeft, minDistRight);
    int midBandDist = midBand(start, mid, end, minDist);	//	중간 Band 영역에서의 최소 거리

    return Math.min(minDist, midBandDist);
  }

  //	중간 Band 영역에서 최소 거리를 구함
  private static int midBand(int start, int mid, int end, int minDist) {
    //	후보 점들을 모아둘 리스트
    List<Point> pointList2 = new ArrayList<Point>();

    //	후보 점들 추출
    int midX = pointList.get(mid).x;	//	mid 점의 x 좌표값
    for(int i = start; i <= end; i++) {
      //	경계(mid)에서의 거리가 minDist보다 작을 경우 후보 점들 리스트에 넣음
      if((midX - pointList.get(i).x) * (midX - pointList.get(i).x) < minDist) {
        pointList2.add(pointList.get(i));
      }
    }

    //	후보 점들을 y 좌표 기준으로 정렬
    Collections.sort(pointList2, sortByY);

    for(int i = 0; i < pointList2.size() - 1; i++) {
      for(int j = i + 1; j < pointList2.size(); j++) {
        Point p1 = pointList2.get(i);	//	y좌표가 작은 순서대로 첫 번째 점 뽑음
        Point p2 = pointList2.get(j);	//	p1 기준으로 y 좌표가 가까운 점들부터 확인

        if((p1.y - p2.y) * (p1.y - p2.y) < minDist) {	//	거리 구해 볼 범위 내에 있으면
          minDist = Math.min(minDist, dist(p1, p2));	//	거리 구해봄
        }
        else {	//	y 좌표 차이가 minDist를 넘어가므로 p1 기준으로 더 찾는 건 의미가 없다.
          break;
        }
      }
    }

    return minDist;
  }
}   //  Main-class-end
```