# 26_03_23_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2176 합리적인 이동경로

[2176번: 합리적인 이동경로](http://boj.ma/2176/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    int n = Integer.parseInt(br.readLine());

    //	Key : 주 이름, Value : 도시 앞 2자리 맵 (Key : 도시 앞 2자리, Value : 그 개수)
    Map<String, Map<String, Integer>> stateMap = new HashMap<>();

    long answer = 0;

    for(int i = 0; i < n; i++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      String cityCode = st.nextToken().substring(0, 2);
      String stateCode = st.nextToken();

      Map<String, Integer> cityMap;
      if(stateMap.containsKey(stateCode))
        cityMap = stateMap.get(stateCode);
      else {
        cityMap = new HashMap<>();
        stateMap.put(stateCode, cityMap);
      }

      cityMap.put(cityCode, cityMap.getOrDefault(cityCode, 0) + 1);
    }

    for(String stateCode1 : stateMap.keySet()) {
      //	stateCode1 주에 해당하는 도시 코드 맵
      Map<String, Integer> cityMap1 = stateMap.get(stateCode1);

      for(String cityCode1 : cityMap1.keySet()) {
        int cnt1 = cityMap1.get(cityCode1);	//	도시 코드가 cityCode1에 해당하는 도시 개수
        //	주 코드가 cityCode1인 주에 해당하는 도시 코드 맵
        Map<String, Integer> cityMap2 = stateMap.get(cityCode1);
        if(stateCode1.equals(cityCode1))
          cityMap2 = null;

        if(cityMap2 != null) {
          int cnt2 = cityMap2.getOrDefault(stateCode1, 0);
          answer += (long)cnt1 * cnt2;
        }
      }
    }

    System.out.println(answer / 2);
  }	//	main-end
}	//	Main-class-end
```