# 25_05_28_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 13502 단어퍼즐 2

[13502번: 단어퍼즐 2](http://boj.ma/13502/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.HashSet;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static final char[][] board = new char[5][5];
	private static final boolean[][] visited = new boolean[5][5];
	private static final Trie trie = new Trie();

	private static final Set<String> wordSet = new HashSet<>();

	public static void main(String[] args) throws Exception {
		StringBuilder sb = new StringBuilder();
		sb.append("AARGH,AARON,ABABA,ABACK,ABANDON,ABANDONED,ABANDONING,ABANDONMENT,ABATEMENT,ABBERLEY");
		//	생략
		sb.append(",ZIMBABWE,ZINC,ZIP,ZODIAC,ZONE,ZONES,ZOO,ZOOLOGICAL,ZOOM,ZOOS,ZURICH,ZUWAYA,ZZAP");
		String raw = sb.toString();
		st = new StringTokenizer(raw, ",");
		while (st.hasMoreTokens())
			trie.addWord(st.nextToken());

		for (int y = 0; y < 5; y++) {
			st = new StringTokenizer(br.readLine());
			for (int x = 0; x < 5; x++)
				board[y][x] = st.nextToken().charAt(0);
		}

		for (int y = 0; y < 5; y++) {
			for (int x = 0; x < 5; x++) {
				dfs(y, x, 1, new char[25]);
			}
		}

		System.out.println(wordSet.size());
	} // main-end

	private static void dfs(int y, int x, int len, char[] word) {
		visited[y][x] = true;
		word[len - 1] = board[y][x];

		int find = trie.find(word, len);

		if (find == -1) {
			visited[y][x] = false;
			return;
		}

		if (find == 1) {
			StringBuilder sb = new StringBuilder();
			for (int i = 0; i < len; i++)
				sb.append(word[i]);
			wordSet.add(sb.toString());
		}

		for (int d = 0; d < 8; d++) {
			int ny = y + dy[d];
			int nx = x + dx[d];

			if (isIn(ny, nx) && !visited[ny][nx])
				dfs(ny, nx, len + 1, word);
		}

		visited[y][x] = false;
	}

	private static final int[] dy = { 0, 1, 1, 1, 0, -1, -1, -1 };
	private static final int[] dx = { 1, 1, 0, -1, -1, -1, 0, 1 };

	private static boolean isIn(int y, int x) {
		return (0 <= y && y < 5) && (0 <= x && x < 5);
	}

	private static class TrieNode {
		private boolean end = false;
		private final TrieNode[] children = new TrieNode[26];
	}

	private static class Trie {
		private TrieNode root = new TrieNode();

		public void addWord(String word) {
			TrieNode cur = this.root;

			for (int i = 0; i < word.length(); i++) {
				char ch = word.charAt(i);

				if (cur.children[ch - 'A'] == null)
					cur.children[ch - 'A'] = new TrieNode();

				if (i == word.length() - 1)
					cur.children[ch - 'A'].end = true;

				cur = cur.children[ch - 'A'];
			}
		}

		// word 단어가 독립적으로 존재하면 1, 접두사로 존재하면 0, 아예 존재하지 않으면 -1
		private int find(char[] word, int len) {
			TrieNode cur = this.root;

			for (int i = 0; i < len; i++) {
				char ch = word[i];

				if (cur.children[ch - 'A'] == null)
					return -1;

				if (i == len - 1) {
					if (cur.children[ch - 'A'].end)
						return 1;
					else
						return 0;
				}

				cur = cur.children[ch - 'A'];
			}

			return -1;
		}
	}
} // Main-class-end
```