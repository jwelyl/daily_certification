# 25_05_29_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 12757 전설의 JBNU

[12757번: 전설의 JBNU](http://boj.ma/12757/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.TreeMap;

public class Main {
	private static final int NONE = -1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int N;
	private static int M;
	private static int K;
	
	private static final TreeMap<Integer, Integer> treeMap = new TreeMap<>();
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		for(int i = 0; i < N; i++) {
			st = new StringTokenizer(br.readLine());
			int key = Integer.parseInt(st.nextToken());
			int value = Integer.parseInt(st.nextToken());
			
			treeMap.put(key, value);
		}
		
		for(int q = 0; q < M; q++) {
			st = new StringTokenizer(br.readLine());
			int opt = Integer.parseInt(st.nextToken());
			int key;
			int value;
			int[] keys;
			
			if(opt == 1) {
				key = Integer.parseInt(st.nextToken());
				value = Integer.parseInt(st.nextToken());
				
				treeMap.put(key, value);
			}
			else if(opt == 2) {
				key = Integer.parseInt(st.nextToken());
				value = Integer.parseInt(st.nextToken());
				
				keys = findKey(key);
				int leftKey = keys[0];
				int rightKey = keys[1];
				
				if(leftKey != NONE || rightKey != NONE) {
					if(leftKey == NONE)
						treeMap.put(rightKey, value);
					else if(rightKey == NONE)
						treeMap.put(leftKey, value);
					else {
						if(key - leftKey > rightKey - key)
							treeMap.put(rightKey, value);
						else if(key - leftKey < rightKey - key)
							treeMap.put(leftKey, value);
					}
				}
			}
			else {
				key = Integer.parseInt(st.nextToken());
				keys = findKey(key);
				
				int leftKey = keys[0];
				int rightKey = keys[1];
				
				if(leftKey == NONE && rightKey == NONE)
					sb.append("-1\n");
				else {
					if(leftKey == NONE)
						sb.append(treeMap.get(rightKey)).append("\n");
					else if(rightKey == NONE)
						sb.append(treeMap.get(leftKey)).append("\n");
					else {
						if(key - leftKey == rightKey - key) {
							if(key - leftKey != 0)
								sb.append("?\n");
							else
								sb.append(treeMap.get(key)).append("\n");
						}
						else if(key - leftKey > rightKey - key)
							sb.append(treeMap.get(rightKey)).append("\n");
						else
							sb.append(treeMap.get(leftKey)).append("\n");
					}
				}
			}
		}
		
		System.out.print(sb);
	} //	main-end
	
	private static int[] findKey(int key) {
		int[] res = {NONE, NONE};
		
		if(treeMap.isEmpty())
			return res;
		
		Integer lowerKey = treeMap.floorKey(key);
		Integer upperKey = treeMap.ceilingKey(key);
		
		if(lowerKey == null) {
			if(upperKey - key <= K)
				res[1] = upperKey;
				
			return res;
		}
		if(upperKey == null) {
			if(key - lowerKey <= K)
				res[0] = lowerKey;
		
			return res;
		}
			
		int lowerDiff = key - lowerKey;
		int upperDiff = upperKey - key;
		
		if(lowerDiff > K && upperDiff > K)
			return res;
		else if(lowerDiff > K) {
			res[1] = upperKey;
			return res;
		}
		else if(upperDiff > K) {
			res[0] = lowerKey;
			return res;
		}
		else {
			res[0] = lowerKey;
			res[1] = upperKey;
			return res;
		}
	}
} //	Main-class-end
```