# 25_06_24_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 20541 앨범정리

[http://boj.ma/20541/t](http://boj.ma/20541/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.TreeMap;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static final Directory root = new Directory("album");
	private static Directory dPointer = root;
	
	private static int N;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		for(int i = 0; i < N; i++) {
			st = new StringTokenizer(br.readLine());
			String cmd = st.nextToken();
			
			if(cmd.equals("mkalb")) {
				String dName = st.nextToken();
				
				if(!dPointer.add(dName, true)) {
					sb.append("duplicated album name\n");
					continue;
				}
			}
			else if(cmd.equals("rmalb")) {
				String targetName = st.nextToken();
				Directory targetDir = null;
				
				int deletedDirCnt = 0;
				int deletedFileCnt = 0;
				
				if(targetName.equals("-1")) {
					if(!dPointer.dChildren.isEmpty()) {
						String firstKey = dPointer.dChildren.firstKey();
						targetDir = dPointer.dChildren.get(firstKey);
						dPointer.dChildren.remove(firstKey);
					}
				}
				else if(targetName.equals("0")) {
					for(Directory dir : dPointer.dChildren.values()) {
						int[] res = dfs(dir);
						deletedDirCnt += res[0];
						deletedFileCnt += res[1];
					}
					
					dPointer.dChildren.clear();
				}
				else if(targetName.equals("1")) {
					if(!dPointer.dChildren.isEmpty()) {
						String lastKey = dPointer.dChildren.lastKey();
						targetDir = dPointer.dChildren.get(lastKey);
						dPointer.dChildren.remove(lastKey);
					}
				}
				else {
					if(dPointer.dChildren.containsKey(targetName)) {
						targetDir = dPointer.dChildren.get(targetName);
						dPointer.dChildren.remove(targetName);
					}
				}
				
				if(targetDir != null) {
					int[] res = dfs(targetDir);
					
					deletedDirCnt = res[0];
					deletedFileCnt = res[1];
				}
				
				sb.append(deletedDirCnt).append(" ").append(deletedFileCnt).append("\n");
			}
			else if(cmd.equals("insert")) {
				String targetName = st.nextToken();
				
				if(!dPointer.add(targetName, false)) {
					sb.append("duplicated photo name\n");
					continue;
				}
			}
			else if(cmd.equals("delete")) {
				String targetName = st.nextToken();
				int deleted = 0;
				
				if(targetName.equals("-1")) {
					if(!dPointer.fChildren.isEmpty()) {
						deleted = 1;
						String firstKey = dPointer.fChildren.firstKey();
						dPointer.fChildren.remove(firstKey);
					}
				}
				else if(targetName.equals("0")) {
					deleted = dPointer.fChildren.size();
					dPointer.fChildren.clear();
				}
				else if(targetName.equals("1")) {
					if(!dPointer.fChildren.isEmpty()) {
						deleted = 1;
						String lastKey = dPointer.fChildren.lastKey();
						dPointer.fChildren.remove(lastKey);
					}
				}
				else {
					if(dPointer.fChildren.containsKey(targetName)) {
						deleted = 1;
						dPointer.fChildren.remove(targetName);
					}
				}
				
				sb.append(deleted).append("\n");
			}
			else {
				String targetName = st.nextToken();
				
				if(targetName.equals("..")) {
					Directory parent = dPointer.parent;
					
					if(parent != null)
						dPointer = parent;
				}
				else if(targetName.equals("/"))
					dPointer = root;
				else {
					if(dPointer.dChildren.containsKey(targetName))
						dPointer = dPointer.dChildren.get(targetName);
				}
				
				sb.append(dPointer.name).append("\n");
			}
		}
		
		System.out.print(sb);
	} //	main-end
	
	private static int[] dfs(Directory dir) {
		int[] res = {1, dir.fChildren.size()};
		
		for(Directory child : dir.dChildren.values()) {
			int[] cres = dfs(child);
			
			res[0] += cres[0];
			res[1] += cres[1];
		}
		
		return res;
	
	}
	
	private static class File {
		public final String name;
		public Directory parent;
	
		public File(String name) {
			this.name = name;
			this.parent = null;
		}
	}
	
	private static class Directory extends File {
		public final TreeMap<String, Directory> dChildren;
		public final TreeMap<String, File> fChildren;
		
		public Directory(String name) {
			super(name);
			dChildren = new TreeMap<>();
			fChildren = new TreeMap<>();
		}
		
		public boolean add(String fileName, boolean isDir) {
			if(isDir) {
				if(this.dChildren.containsKey(fileName))
					return false;

				Directory directory = new Directory(fileName);
				this.dChildren.put(fileName, directory);
				directory.parent = this;
			}
			else {
				if(this.fChildren.containsKey(fileName))
					return false;
				
				File file = new File(fileName);
				this.fChildren.put(fileName, file);
				file.parent = this;
			}
			
			return true;
		}
	}
} //	Main-class-end
```