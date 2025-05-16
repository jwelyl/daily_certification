# 25_05_16_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 14171 Cities and States

[14171번: Cities and States](http://boj.ma/14171/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	
	//	Key : 주 이름, Value : 도시 앞 2자리 맵 (Key : 도시 앞 2자리, Value : 그 개수)
	private static final Map<String, Map<String, Integer>> stateMap = new HashMap<>(); 
	
	private static long answer = 0;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		for(int i = 0; i < N; i++) {
			st = new StringTokenizer(br.readLine());
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