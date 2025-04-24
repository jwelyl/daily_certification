# 25_04_24_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 22861 폴더 정리 (large)

[22861번: 폴더 정리 (large)](http://boj.ma/22861/t)

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
	private static final int MAX = 2_001;
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static final File[] fileArr = new File[MAX];

	private static int N;
	private static int M;
	private static int K;
	private static int Q;

	private static int fileCnt = 0;
	private static Directory root;

	private static final Map<String, Integer> dirNameToDirMap = new HashMap<>();
	private static final Map<String, Integer> parentFileNameToFileMap = new HashMap<>();

	private static final Map<Integer, Integer> fileCountMap = new HashMap<>();
	private static final Map<Integer, Set<String>> fileTypeMap = new HashMap<>();

	public static void main(String[] args) throws IOException {
		init();

		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());

		for (int i = 0; i < N + M; i++) {
			st = new StringTokenizer(br.readLine());
			String dirName = st.nextToken();
			String fileName = st.nextToken();
			boolean isDirectory = Integer.parseInt(st.nextToken()) == 1;

			int parentId;
			Directory parent;
			if (!dirNameToDirMap.containsKey(dirName)) {
				parentId = fileCnt++;
				parent = new Directory(parentId, dirName);
				dirNameToDirMap.put(dirName, parentId);
				fileArr[parentId] = parent;
			} else {
				parentId = dirNameToDirMap.get(dirName);
				parent = (Directory) fileArr[parentId];
			}

			int fileId;
			File file;
			if (isDirectory) {
				if (!dirNameToDirMap.containsKey(fileName)) {
					fileId = fileCnt++;
					file = new Directory(fileId, fileName);
					dirNameToDirMap.put(fileName, fileId);
					fileArr[fileId] = file;
				} else {
					fileId = dirNameToDirMap.get(fileName);
					file = (Directory) fileArr[fileId];
				}
			} else {
				fileId = fileCnt++;
				file = new File(fileId, fileName);
				String parentFileName = parent.getName() + "/" + fileName;
				parentFileNameToFileMap.put(parentFileName, fileId);
				fileArr[fileId] = file;
			}

			parent.addChild(fileId);
		}

		K = Integer.parseInt(br.readLine());
		for (int k = 0; k < K; k++) {
			st = new StringTokenizer(br.readLine());
			String srcPath = st.nextToken();
			String dstPath = st.nextToken();

			int srcDirId = dirNameToDirMap.get(getDirName(srcPath));
			int dstDirId = dirNameToDirMap.get(getDirName(dstPath));

			Directory src = (Directory) fileArr[srcDirId];
			Directory dst = (Directory) fileArr[dstDirId];

			for (int childId : src.getChildren()) {
				File child = fileArr[childId];
				move(child, src, dst);
			}
			src.clearChildren();
		}

		// 전처리
		preprocess(0);

		Q = Integer.parseInt(br.readLine());
		for (int q = 0; q < Q; q++) {
			String path = br.readLine();
			int directoryId = dirNameToDirMap.get(getDirName(path));

			int typeCount = fileTypeMap.getOrDefault(directoryId, Collections.emptySet()).size();
			int totalCount = fileCountMap.getOrDefault(directoryId, 0);

			sb.append(typeCount).append(" ").append(totalCount).append("\n");
		}

		System.out.print(sb);
	}	//	main-end

	private static void preprocess(int fileId) {
		File file = fileArr[fileId];
		if (file.isDeleted()) return;

		if (file.isFile()) {
			fileCountMap.put(fileId, 1);
			Set<String> set = new HashSet<>();
			set.add(file.getName());
			fileTypeMap.put(fileId, set);
			return;
		}

		int count = 0;
		Set<String> types = new HashSet<>();
		Directory dir = (Directory) file;

		for (int childId : dir.getChildren()) {
			preprocess(childId);
			count += fileCountMap.getOrDefault(childId, 0);
			types.addAll(fileTypeMap.getOrDefault(childId, Collections.emptySet()));
		}

		fileCountMap.put(fileId, count);
		fileTypeMap.put(fileId, types);
	}

	private static void move(File movedFile, Directory origParent, Directory newParent) {
		if (movedFile.isFile()) {
			String origParentFileName = origParent.getName() + "/" + movedFile.getName();
			String newParentFileName = newParent.getName() + "/" + movedFile.getName();

			if (parentFileNameToFileMap.containsKey(newParentFileName)) {
				movedFile.setDeleted(true);
				return;
			} else {
				parentFileNameToFileMap.remove(origParentFileName);
				parentFileNameToFileMap.put(newParentFileName, movedFile.getFileId());
			}
		}
		newParent.addChild(movedFile.getFileId());
	}

	private static String getDirName(String path) {
		int idx = path.lastIndexOf('/');
		return path.substring(idx + 1);
	}

	private static void init() {
		int fileId = fileCnt++;
		root = new Directory(fileId, "main");
		dirNameToDirMap.put("main", fileId);
		fileArr[fileId] = root;
	}
}	//	Main-class-end

class File {
	protected final int fileId;
	protected final String name;
	protected boolean isDeleted;

	public File(int fileId, String name) {
		this.fileId = fileId;
		this.name = name;
		this.isDeleted = false;
	}

	public int getFileId() {
		return fileId;
	}

	public String getName() {
		return name;
	}

	public boolean isDeleted() {
		return isDeleted;
	}

	public void setDeleted(boolean isDeleted) {
		this.isDeleted = isDeleted;
	}

	public boolean isFile() {
		return !(this instanceof Directory);
	}

	@Override
	public String toString() {
		return "File [fileId=" + fileId + ", name=" + name + ", isDeleted=" + isDeleted + "]";
	}
}

class Directory extends File {
	private final List<Integer> children;

	public Directory(int fileId, String name) {
		super(fileId, name);
		this.children = new ArrayList<>();
	}

	public List<Integer> getChildren() {
		return children;
	}

	public void addChild(int childId) {
		this.children.add(childId);
	}

	public void clearChildren() {
		this.children.clear();
	}

	@Override
	public String toString() {
		return super.toString() + " / Directory [children=" + children + "]";
	}
}
```