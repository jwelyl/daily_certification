# 24_12_25_daily_certification

```
[#360 koreii] 데일리인증 20241225
1. 코딩 테스트 대비 알고리즘 학습
- Prefix Sum, Coordinate Compression (BOJ 20440)
```

### **BOJ 20440 🎵니가 싫어 싫어 너무 싫어 싫어 오지 마 내게 찝쩍대지마🎵 - 1**

[20440번: 🎵니가 싫어 싫어 너무 싫어 싫어 오지 마 내게 찝쩍대지마🎵 - 1](https://boj.ma/20440/t)

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
	private static int pos = 1;
	
	private static final Set<Integer> set = new HashSet<>();
	private static final List<Integer> coords = new ArrayList<>();
	private static final List<int[]> queries = new ArrayList<>();
	private static final Map<Integer, Integer> map = new HashMap<>();
	private static final Map<Integer, Integer> origMap = new HashMap<>();
	
	private static int[] prefixSum;
	
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        for(int i = 0; i < N; i++) {
        	st = new StringTokenizer(br.readLine());
        	int s = Integer.parseInt(st.nextToken());
        	int e = Integer.parseInt(st.nextToken());
        	
        	if(!set.contains(s)) {
        		set.add(s);
        		coords.add(s);
        	}
        	if(!set.contains(e)) {
        		set.add(e);
        		coords.add(e);
        	}
        	
        	queries.add(new int[] {s, e});
        }
        
        Collections.sort(coords);
        prefixSum = new int[coords.size() + 1];
        
        for(int i = 0; i < coords.size(); i++) {
        	map.put(coords.get(i), i + 1);
        	origMap.put(i + 1, coords.get(i));
        }
        
        for(int[] query : queries) {
        	int s = map.get(query[0]);
        	int e = map.get(query[1]);
        	
        	prefixSum[s]++;
        	prefixSum[e]--;
        }
        
        for(int i = 1; i <= coords.size(); i++)
        	prefixSum[i] = prefixSum[i - 1] + prefixSum[i];
        
        int start = 1;
        int end = 1;
        int cnt = prefixSum[end];
        int maxStart = 1;
        int maxEnd = 1;
        int maxCnt = cnt;
        
        while(true) { 	
        	if(end + 1 > coords.size())
        		break;

        	if(prefixSum[end + 1] != cnt) {	//	구간 끊길 경우
        		start = end + 1;
        		end = start;
        		cnt = prefixSum[end];
        	}
        	else	//	구간 이어질 경우
        		end = end + 1;
        	
	        if(cnt > maxCnt) {
	        	maxCnt = cnt;
	        	maxStart = start;
	        	maxEnd = end;
	        }
	        else if(cnt == maxCnt && start ==  maxStart && end > maxEnd)
	        	maxEnd = end;
        }
        
        System.out.println(maxCnt);
        System.out.println(origMap.get(maxStart) + " " + origMap.get(maxEnd + 1));
    }    //    main-end
}    //    Main-class-end
```