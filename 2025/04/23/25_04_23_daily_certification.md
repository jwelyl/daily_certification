# 25_04_23_daily_certification

```
[#113 koreii] 데일리인증 20250423
0. 상반기 현황
- 면접
1. 코딩 테스트 대비 문제 풀이
- DP (BOJ 5557 1학년)
- DFS, Tree, HashMap (BOJ 22861 폴더 정리 (large) 풀이 중) 
```

# Problem Solving (Algorithm & SQL)

### BOJ 5557 1학년

[5557번: 1학년](http://boj.ma/5557/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX = 20;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;			//	숫자 개수
    private static int[] digits;	//	digits[i] : i번째 숫자
    
    private static long[][] dp;		//	dp[i][j] : 1 ~ j번째 수의 연산 결과가 i가 되는 경우의 수

    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        digits = new int[N + 1];
        st = new StringTokenizer(br.readLine());
        for(int i = 1; i <= N; i++)
        	digits[i] = Integer.parseInt(st.nextToken());
        
        dp = new long[MAX + 1][N];
        
        //	1번째 수의 결과는 digits[1]
        dp[digits[1]][1] = 1;
        
        for(int i = 2; i < N; i++) {	//	1 ~ i번까지 숫자를 적절히 더하거나 빼보기
        	int digit = digits[i];		//	i번째 숫자
        	
        	for(int j = 0; j <= MAX; j++) {
        		int add = j + digit;
        		int sub = j - digit;
        		
        		if(0 <= add && add <= MAX)	//	i-1번째 숫자까지의 결과에 i번째 수를 더한 결과가 유효할 경우
        			dp[add][i] += dp[j][i - 1];
        		if(0 <= sub && sub <= MAX)	//	i-1번째 숫자까지의 결과에 i번째 수를 뺀 결과가 유효할 경우
        			dp[sub][i] += dp[j][i - 1];
        	}
        }
        
        System.out.println(dp[digits[N]][N - 1]);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 22861 폴더 정리 (large)

[22861번: 폴더 정리 (large)](http://boj.ma/22861/t)

**실패한 코드 (런타임에러 못 잡음)**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.HashSet;
import java.util.List;
import java.util.Map;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	//	전체 파일 리스트
	private static final List<File> fileList = new ArrayList<>();
	//	K : 파일 절대 경로, V : 파일 고유 번호
	private static final Map<String, Integer> pathToFIdMap = new HashMap<>();
	//	K : 파일 고유 번호, V : 파일 절대 경로
	private static final Map<Integer, String> fIdToPathMap = new HashMap<>();
	
	private static final Map<String, String> dirNameToPathMap = new HashMap<>();
	
	private static int N;	//	디렉토리 총 개수
	private static int M;	//	파일 총 개수
	private static int K;	//	정리 횟수
	private static int Q;	//	질의 개수
	
	public static void main(String[] args) throws IOException {
		init();
		
//		System.out.println(root);
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		for(int i = 0; i < N + M; i++) {
			st = new StringTokenizer(br.readLine());
			String dirName = st.nextToken();					//	상위 디렉토리 이름
			String fileName = st.nextToken();					//	하위 파일 이름
			int dir = Integer.parseInt(st.nextToken());			//	하위 파일이 디렉토리인지
			
			File directory = getDirectory(dirName);							//	상위 디렉토리
			String filePath = directory.getFilePath() + "/" + fileName;		//	하위 파일 절대경로 = 상위 디렉토리 절대경로 + "/" + 하위 파일 이름
			File file = createFile(fileName, filePath, dir);				//	하위 파일
			
			directory.addChild(file);
			if(file.isDirectory())
				dirNameToPathMap.put(fileName, filePath);
		}
		
//		System.out.println(root);
//		System.out.println("after " + (N + M) + " file input");
//		dfs(root);
		
		K = Integer.parseInt(br.readLine());
		for(int k = 0; k < K; k++) {
			st = new StringTokenizer(br.readLine());
			String fromPath = st.nextToken();	//	절대경로가 fromPath인 디렉토리
			String toPath = st.nextToken();		//	절대경로가 toPath인 디렉토리
			
			move(fromPath, toPath);
		}
		
//		System.out.println("after " + K + " moves");
//		dfs(root);
		
		Q = Integer.parseInt(br.readLine());
		for(int q = 0; q < Q; q++) {
			String dirPath = br.readLine();
			File directory = getFile(dirPath);
			
			Set<String> fileNameSet = new HashSet<>();
			int fileCnt = search(directory, fileNameSet);
			
			sb.append(fileNameSet.size()).append(" ").append(fileCnt).append("\n");
		}
		
		System.out.print(sb);
	} //	main-end
	
	private static void init() {
		createFile("main", "main", 1);
		dirNameToPathMap.put("main", "main");
	}
	
	private static int search(File file, Set<String> fileNameSet) {
		int res = 0;
		
		if(file.isDirectory()) {
			for(File child : file.getChildren())
				res += search(child, fileNameSet);
		}
		else if(!file.isDirectory() && !file.isDeleted()) {
			res = 1;
			fileNameSet.add(file.getFileName());
		}
		
		return res;
	}
	
	//	절대경로가 fromPath에 해당하는 디렉토리의 하위 파일들을 전부 절대경로가 toPath에 해당하는 디렉토리로 옮김
	private static void move(String fromPath, String toPath) {
		File fromDir = getFile(fromPath);
		File toDir = getFile(toPath);
		
		for(File child : fromDir.getChildren()) {
			toDir.addChild(child);
			move(child, toDir);
		}
	
		fromDir.getChildren().clear();	//	fromDir의 하위 파일들을 모두 제거
	}
	
	//	target 파일을 dest 디렉토리의 하위로 옮김
	private static void move(File target, File dest) {
		String targetName = target.getFileName();
		String targetNewPath = dest.getFilePath() + "/" + targetName;	//	파일을 옮겼을때 새로운 절대경로
		
		if(pathToFIdMap.containsKey(targetNewPath))	{	//	이미 중복된 파일이 존재할 경우
			target.setDeleted(true);	//	중복된 파일 제거
			return;
		}
		
		modifyPath(target, targetNewPath);
		
		if(target.isDirectory()) {
			for(File child : target.getChildren())
				move(child, target);
		}
	}
	
	private static File createFile(String fileName, String filePath, int directory) {
		int fId = fileList.size();	//	파일 고유번호 = 파일 생성 당시 파일 개수 (0부터 시작)
		File file = new File(fId, fileName, filePath, directory);
		
		pathToFIdMap.put(filePath, fId);
		fIdToPathMap.put(fId, filePath);
		fileList.add(file);
		
		return file;
	}
	
	//	dirName을 가지는 디렉토리 찾기 (모든 디렉토리는 이름이 고유하다고 가정)
	private static File getDirectory(String dirName) {
		String dirPath = dirNameToPathMap.get(dirName);	//	해당 디렉토리의 절대경로 찾음
		return getFile(dirPath);
	}
	
	private static File getFile(String path) {
		int fId = pathToFIdMap.get(path);
		
		return fileList.get(fId);
	}
	
	private static void modifyPath(File file, String afterPath) {
		int fId = file.getfId();
		String beforePath = file.getFilePath();
		
		file.setFilePath(afterPath);
		
		pathToFIdMap.remove(beforePath);
		pathToFIdMap.put(afterPath, fId);
		fIdToPathMap.put(fId, afterPath);
	}
	
	private static void modifyPath(String beforePath, String afterPath) {
		int fId = pathToFIdMap.get(beforePath);
		File file = fileList.get(fId);
		
		file.setFilePath(afterPath);
	
		pathToFIdMap.remove(beforePath);
		pathToFIdMap.put(afterPath, fId);
		fIdToPathMap.put(fId, afterPath);
	}
	
	private static void dfs(File file) {
		if(file.isDeleted())
			return;
		
		System.out.println(file);
		
		if(file.isDirectory()) {
			for(File child : file.getChildren())
				dfs(child);
		}
	}
} //	Main-class-end

class File {
	private final int fId;					//	파일 식별자
	private final String fileName;			//	파일 이름
	private String filePath;				//	main으로부터 절대경로
	private final boolean directory;		//	true면 디렉토리, false면 파일
	private final List<File> children;		//	디렉토리일 경우 하위의 파일 저장할 수 있는 리스트, 아닐 경우 null
	private boolean deleted;
	
	public File(int fId, String fileName, String filePath, int directory) {
		this.fId = fId;
		this.fileName = fileName;
		this.filePath = filePath;
		this.directory = directory == 1;
		this.children = this.directory ? new ArrayList<>() : null;
		this.deleted = false;
	}
	
	public void addChild(File child) {
		if(!this.isDirectory())
			return;
		
		this.children.add(child);
	}
	
	//	파일 절대경로가 아닌 파일 자체 이름만 반환
	public String getFileName() {
		return this.fileName;
	}

	public String getFilePath() {
		return filePath;
	}

	//	파일을 옮겨서 절대경로가 변경될 경우
	public void setFilePath(String filePath) {
		this.filePath = filePath;
	}

	public int getfId() {
		return fId;
	}

	public boolean isDirectory() {
		return directory;
	}

	public List<File> getChildren() {
		return children;
	}
	
	public boolean isDeleted() {
		return deleted;
	}

	public void setDeleted(boolean deleted) {
		this.deleted = deleted;
	}

	@Override
	public String toString() {
		return (this.directory ? "Directory " : "File ") + this.fId + " : " + this.filePath + ", childrens : " + (this.children == null ? -1 : this.children.size());
	}
}
```