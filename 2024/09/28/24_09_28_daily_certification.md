# 24_09_28_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 5389 제곱근 작도

[](https://www.acmicpc.net/problem/5389)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    
    private static int T;
    private static int N;
    
    public static void main(String[] args) throws IOException {
        T = Integer.parseInt(br.readLine());
        
        for(int tc = 1; tc <= T; tc++) {
            N = Integer.parseInt(br.readLine());
            
            int sqrt = (int)Math.sqrt(N);
            if(sqrt * sqrt == N) {
                sb.append("0 ").append(sqrt).append("\n");
                continue;
            }
            
            int minX = Integer.MAX_VALUE;
            int minY = 0;
            
            for(int num = 1; num <= sqrt; num++) {
                if(N % num == 0) {
                    int yMinusX = num;        //    y - x 값
                    int yPlusX = N / num;     //    y + x 값
                    
                    if((-yMinusX + yPlusX) % 2 == 0 && (yMinusX + yPlusX) % 2 == 0) {
                    	int x = (-yMinusX + yPlusX) / 2;
                    	int y = (yMinusX + yPlusX) / 2;
                    	
                    	if(x < minX) {
                    		minX = x;
                    		minY = y;
                    	}
                    }
                }
            }
            
            sb.append(minX == Integer.MAX_VALUE ? "IMPOSSIBLE\n" : String.format("%d %d\n", minX, minY));
        }
        
        System.out.print(sb);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 2015 수들의 합 4

[](https://www.acmicpc.net/problem/2015)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.Map;
import java.util.HashMap;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    정수 개수
    private static int K;    //    찾으려는 부분합
    private static Map<Integer, Integer> map = new HashMap<>();
    
    private static long ans = 0;
    
    public static void main(String[] args) throws IOException {
        map.put(0, 1);
        
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());        
        
        int sum = 0;    //    누적합
        st = new StringTokenizer(br.readLine());
        for(int i = 1; i <= N; i++) {
            sum += Integer.parseInt(st.nextToken());    //    현재 누적합
            
            //    현재 누적합 [1, i] = sum일 때 부분합이 K이려면 이전 누적합 [i, j] (1 <= j < i) 중 그 값이 sum - K인 것이 존재해야 함 
            ans += map.getOrDefault(sum - K, 0);
            
            map.put(sum, map.getOrDefault(sum, 0) + 1);
        }
        
        System.out.println(ans);
    }    //    main-end
}    //    Main-class-end
```

### 삼성전자 2023년 상반기 오전 2번 토끼와 경주

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/training-field/frequent-problems/problems/rabit-and-race/description?page=3&pageSize=5)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;
import java.util.PriorityQueue;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
	private static final int INIT = 100;		//	경주 시작 준비
	private static final int RACE = 200;		//	경주 진행
	private static final int MODIFY = 300;		//	이동거리 변경
	private static final int RESULT = 400;		//	최고의 토끼 선정
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int Q;	//	명령어 개수
	
	//	INIT
	private static int N;	//	격자 행 개수
	private static int M;	//	격자 열 개수
	private static int P;	//	토끼 마릿수
	
	//	RACE
	private static int K;	//	경주 당 턴 수
	private static int S;	//	추가 점수
	
	//	MODIFY
	private static int PID;	//	이동거리 변경할 토끼 고유번호
	private static int L;	//	해당 토끼 현재 이동거리에 곱할 값
	
	private static Rabbit[] rabbitArr;	// rabbitArr[i] : i번째 토끼
	private static final PriorityQueue<Rabbit> pq = new PriorityQueue<>();
	
	//	Key : PID, Value : pid 값이 PID인 토끼가 rabbitArr에 저장된 인덱스 idx
	private static final Map<Integer, Integer> pidToIdxMap = new HashMap<>();
	
	public static void main(String[] args) throws IOException {
		Q = Integer.parseInt(br.readLine());
		
		for(int q = 1; q <= Q; q++)
			processCommand();
	}	//	main-end
	
	private static void processCommand() throws IOException {
		st = new StringTokenizer(br.readLine());
		
		int cmd = Integer.parseInt(st.nextToken());
		
		switch(cmd) {
		case INIT:
			init();
			break;
		case RACE:
			race();
			break;
		case MODIFY:
			modify();
			break;
		case RESULT:
			result();
			break;
		}
	}
	
	private static void init() {
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		P = Integer.parseInt(st.nextToken());
		
		rabbitArr = new Rabbit[P + 1];
		for(int idx = 1; idx <= P; idx++) {
			int pid = Integer.parseInt(st.nextToken());
			int d = Integer.parseInt(st.nextToken());
			
			Rabbit rabbit = new Rabbit(pid,d, idx);
			
			pidToIdxMap.put(pid, idx);	//	고유번호가 pid인 토끼 rabbit은 rabbitArr의 index idx에 저장
			rabbitArr[idx] = rabbit;		//	rabbitArr에 저장
			pq.offer(rabbit);			//	우선순위 큐에도 저장
		}
	}
	
	private static void race() {
		K = Integer.parseInt(st.nextToken());
		S = Integer.parseInt(st.nextToken());
		
		//	exceptions[k][0/1] : {k번째 턴에 점수 합에서 제외되는 토끼 idx / 제외해야 할 점수} 
		int[][] exceptions = new int[K + 1][2];
		int sum = 0;	//	매 턴에 추가되는 점수 합
		Set<Integer> selectedIdxSet = new HashSet<>();	//	선택되어 뛴 적 있는 토끼 index
		
		for(int k = 1; k <= K; k++) {
			Rabbit rabbit = pq.poll();	//	이번 턴에 뛸 토끼
			int curDist = rabbit.d;		//	이번 턴에 뛸 토끼가 뛰어야 하는 거리
			int curIdx = rabbit.idx;	//	이번 턴에 뛸 토끼의 index
			int curRow = rabbit.r;
			int curCol = rabbit.c;	//	이번 턴에 뛸 토끼의 초기 위치
			
			int lastRow = 0;
			int lastCol = 0;	//	이번 턴에 뛸 토끼가 최종적으로 위치할 행, 열
			
			for(int dir = 0; dir < 4; dir++) {
				int[] nextPos = getNextPos(curRow, curCol, curDist, dir);
				int nextRow = nextPos[0];
				int nextCol = nextPos[1];	//	dir 방향으로 점프했을 때의 최종 위치
				
				if(nextRow + nextCol > lastRow + lastCol) {
					lastRow = nextRow;
					lastCol = nextCol;
				}
				else if((nextRow + nextCol == lastRow + lastCol) && (nextRow > lastRow)) {
					lastRow = nextRow;
					lastCol = nextCol;
				}
			}
			
			rabbit.r = lastRow;
			rabbit.c = lastCol;	//	이번 턴에서 뛴 토끼 위치 최종적으로 확정
			rabbit.jumpCnt++;	//	이번 턴에서 뛴 토끼 점프 횟수 1 증가
			
			pq.offer(rabbit);	//	다시 우선순위큐 삽입
			
			sum += (lastRow + lastCol);	//	이번 턴에서 rabbit을 제외한 토끼들이 얻을 점수
			exceptions[k][0] = curIdx;	//	이번 턴에서 점수 추가에 제외되는 rabbit의 index
			exceptions[k][1] = lastRow + lastCol;	//	이번 턴에서 점수 추가에 제외되는 rabbit의 제외 점수
			
			selectedIdxSet.add(curIdx);
		}
		
		//	일단 모든 토끼에게 각 턴에서 발생한 모든 점수를 더함
		for(int idx = 1; idx <= P; idx++)
			rabbitArr[idx].score += sum;
		
		for(int k = 1; k <= K; k++) {
			int[] exception = exceptions[k];
			int idx = exception[0];		//	제외될 토끼 idx
			int score = exception[1];	//	제외할 점수
			
			rabbitArr[idx].score -= score;
		}
		
		addExtraScore(S, selectedIdxSet);
	}
	
	private static int[] getNextPos(int curRow, int curCol, int jumpDist, int dir) {
		int nextRow = 0;
		int nextCol = 0;
		int len = (dir == 0 || dir == 1) ? M - 1 : N - 1;
		
		if(dir == 0) {	//	오른쪽으로 이동할 경우
			nextRow = curRow;	//	행 변화는 없음
			
			if(curCol + jumpDist <= M)	//	오른쪽으로만 jumpDist만큼 갔을 때 격자를 벗어나지 않는 경우
				nextCol = curCol + jumpDist;
			else {	//	오른쪽으로만 jumpDist만큼 갔을 때 격자를 벗어나는 경우
				jumpDist -= (M - curCol);	//	오른쪽 끝까지 가고 남은 이동 수
				
				int q = jumpDist / len;
				int r = jumpDist % len;
				
				if(q % 2 == 0)	//	jumpDist 중 q * len만큼 뛰어서 오른쪽 끝에 도착한 경우 
					nextCol = M - r;	//	오른쪽 끝에서 r만큼 왼쪽으로 뛰면 됨
				else			//	jumpDist 중 q * len만큼 뛰어서 왼쪽 끝에 도착한 경우 
					nextCol = 1 + r;	//	왼쪽 끝에서 r만큼 오른쪽으로 뛰면 됨
			}
		}
		else if(dir == 1) {	//	왼쪽으로 이동할 경우
			nextRow = curRow;	//	행 변화는 없음
			
			if(curCol - jumpDist >= 1)	//	왼쪽으로만 jumpDist만큼 갔을 때 격자를 벗어나지 않는 경우
				nextCol = curCol - jumpDist;
			else {	//	왼쪽으로만 jumpDist만큼 갔을 때 격자를 벗어나는 경우
				jumpDist -= (curCol - 1);	//	왼쪽 끝까지 가고 남은 이동 수
				
				int q = jumpDist / len;
				int r = jumpDist % len;
				
				if(q % 2 == 0)	//	jumpDist 중 q * len만큼 뛰어서 왼쪽 끝에 도착한 경우 
					nextCol = 1 + r;	//	왼쪽 끝에서 r만큼 오른쪽으로 뛰면 됨
				else			//	jumpDist 중 q * len만큼 뛰어서 오른쪽 끝에 도착한 경우 
					nextCol = M - r;	//	오른쪽 끝에서 r만큼 왼쪽으로 뛰면 됨
			}
		}
		else if(dir == 2) {	//	아래쪽으로 이동할 경우
			nextCol = curCol;	//	열 변화는 없음
			
			if(curRow + jumpDist <= N)	//	아래쪽으로만 jumpDist만큼 갔을 때 격자를 벗어나지 않는 경우
				nextRow = curRow + jumpDist;
			else {	//	아래쪽으로만 jumpDist만큼 갔을 때 격자를 벗어나는 경우
				jumpDist -= (N - curRow);	//	아래쪽 끝까지 가고 남은 이동 수
				
				int q = jumpDist / len;
				int r = jumpDist % len;
				
				if(q % 2 == 0)	//	jumpDist 중 q * len만큼 뛰어서 아래쪽 끝에 도착한 경우 
					nextRow = N - r;	//	아래쪽 끝에서 r만큼 위쪽으로 뛰면 됨
				else			//	jumpDist 중 q * len만큼 뛰어서 위쪽 끝에 도착한 경우 
					nextRow = 1 + r;	//	위쪽 끝에서 r만큼 아래쪽으로 뛰면 됨
			}
		}
		else {	//	위쪽으로 이동할 경우
			nextCol = curCol;	//	열 변화는 없음
			
			if(curRow - jumpDist >= N)	//	위쪽으로만 jumpDist만큼 갔을 때 격자를 벗어나지 않는 경우
				nextRow = curRow - jumpDist;
			else {	//	위쪽으로만 jumpDist만큼 갔을 때 격자를 벗어나는 경우
				jumpDist -= (curRow - 1);	//	위쪽 끝까지 가고 남은 이동 수
				
				int q = jumpDist / len;
				int r = jumpDist % len;
				
				if(q % 2 == 0)	//	jumpDist 중 q * len만큼 뛰어서 위쪽 끝에 도착한 경우 
					nextRow = 1 + r;	//	위쪽 끝에서 r만큼 아래쪽으로 뛰면 됨
				else			//	jumpDist 중 q * len만큼 뛰어서 아래쪽 끝에 도착한 경우 
					nextRow = N - r;	//	아래쪽 끝에서 r만큼 위쪽으로 뛰면 됨
			}
		}
		
		return new int[] {nextRow, nextCol};
	}
	
	private static void addExtraScore(int add, Set<Integer> selectedIdxSet) {
		//		K 턴 모두 끝나고 추가 점수 S 받을 토끼 찾기
		int sIdx = 0;	//	추가 점수 받을 토끼의 index
		int sRow = 0;
		int sCol = 0;	//	추가 점수 받을 토끼의 행, 열 위치
		int sPid = 0;	//	추가 점수 받을 토끼의 고유번호
		
		for(int idx = 1; idx <= P; idx++) {
			if(!selectedIdxSet.contains(idx))	//	해당 index 토끼가 지난 K턴 동안 한번도 선택 받은 적 없을 경우
				continue;
			
			int r = rabbitArr[idx].r;
			int c = rabbitArr[idx].c;
			int pid = rabbitArr[idx].pid;
			
			if(r + c > sRow + sCol) {	//	1. 행 + 열 값 큰 토끼
				sIdx = idx;
				sRow = r;
				sCol = c;
				sPid = pid;
			}
			else if(r + c == sRow + sCol) {	//	1을 만족하는 토끼가 여럿일 경우
				if(r > sRow) {	//	2. 행 값 큰 토끼
					sIdx = idx;
					sRow = r;
					sCol = c;
					sPid = pid;
				}
				else if(r == sRow && sPid > pid) {	//	1, 2를 만족하는 토끼가 여럿일 경우, 3. pid 값 큰 토끼
					sIdx = idx;
					sPid = pid;
				}
			}
		}
		
		rabbitArr[sIdx].score += add;
	}
	
	private static void modify() {
		PID = Integer.parseInt(st.nextToken());
		L = Integer.parseInt(st.nextToken());
	
		int idx = pidToIdxMap.get(PID);	//	고유번호가 PID인 토끼의 rabbitArr에서의 위치
		rabbitArr[idx].d *= L;	//	해당 토끼 이동해야 하는 거리 L배
	}
	
	private static void result() {
		long maxScore = 0;
		
		for(int idx = 1; idx <= P; idx++) {
			Rabbit rabbit = rabbitArr[idx];
			
			if(rabbit.score > maxScore)
				maxScore = rabbit.score;
		}
		
		System.out.println(maxScore);
	}
	
	private static class Rabbit implements Comparable<Rabbit> {
		public int pid;		//	토끼 고유번호
		public int d;		//	토끼가 뛰어야 하는 칸의 수
		public int idx;
		public int r;
		public int c;		//	토끼의 현재 위치
		
		public int jumpCnt;	//	토끼가 현재까지 점프한 횟수
		public long score;	//	해당 토끼의 점수
		
		public Rabbit(int pid, int d, int idx) {
			this.pid = pid;
			this.d = d;
			this.idx = idx;
			this.r = 1;
			this.c = 1;	//	모든 토끼는 초기에 (1, 1)에 위치
			
			this.jumpCnt = 0;
			this.score = 0;
		}
		
		@Override
		public int compareTo(Rabbit other) {
			//	1. 총 점프횟수가 적은 토끼
			int ret = Integer.compare(this.jumpCnt, other.jumpCnt);
		
			if(ret == 0) {	//	1을 만족하는 토끼가 여럿일 경우
				//	2. 위치한 행 + 열 번호가 작은 토끼
				ret = Integer.compare(this.r + this.c, other.r + other.c);
				
				if(ret == 0) {	//	1, 2를 만족하는 토끼가 여럿일 경우
					//	3. 위치한 행 번호가 작은 토끼
					ret = Integer.compare(this.r, other.r);
					
					if(ret == 0)	//	1, 2, 3을 만족하는 토끼가 여럿일 경우
						//	4. 고유번호가 작은 토끼
						ret = Integer.compare(this.pid, other.pid);
				}
				
			}
			
			return ret;
		}
	}
}	//	Main-class-end

```

### 삼성전자 2023년 하반기 오후 2번 산타의 선물 공장 2

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/training-field/frequent-problems/problems/santa-gift-factory-2/description?page=3&pageSize=5)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int DUMMY = -1;		//	HEAD, TAIL DUMMY node
	
	private static final int INIT = 100;		//	공장 설립
	private static final int TRANSFER = 200;	//	물건 모두 옮기기
	private static final int SWAP = 300;		//	앞 물건만 교체하기
	private static final int DIVIDE = 400;		//	물건 나누기
	private static final int PRESENT = 500;		//	선물 정보 얻기
	private static final int BELT = 600;		//	벨트 정보 얻기
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int Q;	//	명령어 개수
	
	private static int N;	//	벨트 개수
	private static int M;	//	선물 개수
	
	private static Node[] heads;	//	heads[i] : i번째 벨트의 head
	private static Node[] tails;	//	tails[i] : i번째 벨트의 tail
	private static int[] cnts;		//	cnts[i] : i번째 벨트에 존재하는 선물 개수
	
	private static Node[] presents;	//	presents[i] : pNum = i인 선물의 Node 객체
	
	public static void main(String[] args) throws IOException {
		Q = Integer.parseInt(br.readLine());
		
		for(int q = 1; q <= Q; q++)
			processCommand();
		
		System.out.print(sb);
	}	//	main-end
	
	private static void processCommand() throws IOException {
		st = new StringTokenizer(br.readLine());
		
		int cmd = Integer.parseInt(st.nextToken());
		
		switch(cmd) {
		case INIT:
			init();
			break;
		case TRANSFER:
			transfer();
			break;
		case SWAP:
			swap();
			break;
		case DIVIDE:
			divide();
			break;
		case PRESENT:
			present();
			break;
		case BELT:
			belt();
			break;
		}
	}
	
	private static void init() {
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		initBelt();
		
		presents = new Node[M + 1];
		
		for(int pNum = 1; pNum <= M; pNum++) {
			Node node = new Node(pNum);
			int bNum = Integer.parseInt(st.nextToken());
			
			presents[pNum] = node;
			pushBack(bNum, node);
		}
	}
	
	private static void initBelt() {
		cnts = new int[N + 1];
		heads = new Node[N + 1];
		tails = new Node[N + 1];
		
		for(int bNum = 1; bNum <= N; bNum++) {
			heads[bNum] = new Node(DUMMY);
			tails[bNum] = new Node(DUMMY);
			
			heads[bNum].next = tails[bNum];
			tails[bNum].prev = heads[bNum];
		}
	}
	
	//	bNum번째 벨트의 가장 마지막에 node를 삽입
	private static void pushBack(int bNum, Node node) {
		Node last = tails[bNum].prev;
		
		last.next = node;
		tails[bNum].prev = node;
		node.prev = last;
		node.next = tails[bNum];
		
		cnts[bNum]++;
	}
	
	//	bNum번째 벨트의 가장 처음에 node를 삽입
	private static void pushFront(int bNum, Node node) {
		Node first = heads[bNum].next;
		
		first.prev = node;
		heads[bNum].next = node;
		node.prev = heads[bNum];
		node.next = first;
		
		cnts[bNum]++;
	}
	
	private static void transfer() {
		int mSrc = Integer.parseInt(st.nextToken());
		int mDst = Integer.parseInt(st.nextToken());
		
		if(cnts[mSrc] != 0) {
			Node srcFront = heads[mSrc].next;
			Node srcBack = tails[mSrc].prev;
			
			Node dstFront = heads[mDst].next;
			
			heads[mDst].next = srcFront;
			srcFront.prev = heads[mDst];
			
			dstFront.prev = srcBack;
			srcBack.next = dstFront;
			
			heads[mSrc].next = tails[mSrc];
			tails[mSrc].prev = heads[mSrc];
			
			cnts[mDst] += cnts[mSrc];
			cnts[mSrc] = 0;
		}
		
		sb.append(cnts[mDst]).append("\n");
	}
	
	private static Node popFront(int bNum) {
		if(cnts[bNum] == 0)
			return null;
		
		Node front = heads[bNum].next;
		Node nFront = front.next;
		
		heads[bNum].next = nFront;
		nFront.prev = heads[bNum];
		cnts[bNum]--;
		
		return front;
	}
	
	private static void swap() {
		int mSrc = Integer.parseInt(st.nextToken());
		int mDst = Integer.parseInt(st.nextToken());
		
		Node srcFront = popFront(mSrc);
		Node dstFront = popFront(mDst);
		
		if(srcFront != null)
			pushFront(mDst, srcFront);
		if(dstFront != null)
			pushFront(mSrc, dstFront);
		
		sb.append(cnts[mDst]).append("\n");
	}
	
	private static void divide() {
		int mSrc = Integer.parseInt(st.nextToken());
		int mDst = Integer.parseInt(st.nextToken());
		
		int cnt = cnts[mSrc] / 2;	//	mSrc 벨트에서 옮길 개수
		
		Node first = heads[mSrc].next;
		Node last = heads[mSrc];
		
		for(int i = 0; i < cnt; i++)
			last = last.next;
		
		Node nLast = last.next;
		heads[mSrc].next = nLast;
		nLast.prev = heads[mSrc];
		
		if(last.pNum != DUMMY) {	//	옮길 선물이 있는 경우
			Node dstFront = heads[mDst].next;
			
			heads[mDst].next = first;
			first.prev = heads[mDst];
			
			last.next = dstFront;
			dstFront.prev = last;
		}
		
		cnts[mSrc] -= cnt;
		cnts[mDst] += cnt;
		
		sb.append(cnts[mDst]).append("\n");
	}
	
	private static void present() {
		int pNum = Integer.parseInt(st.nextToken());
		
		Node node = presents[pNum];
		
		Node prev = node.prev;
		Node next = node.next;
		
		int a = prev.pNum;
		int b = next.pNum;
		
		sb.append(a + 2 * b).append("\n");
	}
	
	private static void belt() {
		int bNum = Integer.parseInt(st.nextToken());
		
		Node front = heads[bNum].next;
		Node back = tails[bNum].prev;
		
		int a = front.pNum;
		int b = back.pNum;
		int c = cnts[bNum];
		
		sb.append(a + 2 * b + 3 * c).append("\n");
	}
	
	private static class Node {
		public final int pNum;	//	선물 번호 (1 ~ M)
		public Node prev;
		public Node next;
		
		public Node(int pNum) {
			this.pNum = pNum;
			this.prev = null;
			this.next = null;
		}
	}
}	//	Main-class-end
```