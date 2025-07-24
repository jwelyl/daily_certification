# 25_07_25_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 8913 문자열 뽑기

[8913번: 문자열 뽑기](http://boj.ma/8913/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();

	private static int T;	//	테스트케이스 개수
	
	private static char[] input;
	private static final List<Chunk> chunks = new ArrayList<>();
	
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		T = Integer.parseInt(br.readLine());
		
		for(int tc = 1; tc <= T; tc++) {
			input = br.readLine().toCharArray();
			chunks.clear();
			answer = 0;
			
			char before = ' ';
			Chunk cur = null;
			
			for(char ch : input) {
				if(ch != before) {
					if(cur != null)
						chunks.add(cur);
						
					cur = new Chunk(ch);
				}
				else
					cur.cnt++;
				
				before = ch;
			}
			chunks.add(cur);
			
			backtracking(chunks);
			sb.append(answer).append("\n");
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static void backtracking(List<Chunk> chunkList) {
		if(answer == 1)
			return;
		
		if(chunkList.size() == 1) {
			if(chunkList.get(0).cnt >= 2)
				answer = 1;
			return;
		} 
		
		for(int i = 0; i < chunkList.size(); i++) {
			Chunk cur = chunkList.get(i);
			
			if(cur.cnt >= 2) {
				List<Chunk> nChunkList = new ArrayList<>();
				
				if(i == 0)
					nChunkList = chunkList.subList(1, chunkList.size());
				else if(i == chunkList.size() - 1)
					nChunkList = chunkList.subList(0, chunkList.size() - 1);
				else {
					for(int j = 0; j < i - 1; j++)
						nChunkList.add(chunkList.get(j));
					
					Chunk merged = new Chunk(chunkList.get(i - 1).ch);
					merged.cnt = chunkList.get(i - 1).cnt + chunkList.get(i + 1).cnt;
					nChunkList.add(merged);
					
					for(int j = i + 2; j < chunkList.size(); j++)
						nChunkList.add(chunkList.get(j));
				}
				
				backtracking(nChunkList);
			}
		}
	}
	
	private static class Chunk {
		public char ch;
		public int cnt;
		
		public Chunk(char ch) {
			this.ch = ch;
			this.cnt = 1;
		}
	}
}	//	Main-class-end
```