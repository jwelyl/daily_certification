# 25_03_14_daily_certification

# Problem Solving (Algorithm & SQL)

### **CO{)E TREE** 색깔 트리

[Code Tree | Learning to Code with Confidence](https://www.codetree.ai/ko/frequent-problems/problems/color-tree/description?introductionSetId=&bookmarkId=)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static final int NONE = -1;				//	부모 노드가 없음
	private static final int MAX_NODE = 100_000;	//	최대 노드 개수
	
	private static final List<Integer> rootList = new ArrayList<>();
	
	private static final List<Integer>[] tree = new ArrayList[MAX_NODE + 1];
	
	private static final int[] colors = new int[MAX_NODE + 1];		//	colors[v] : v 노드의 색깔
	private static final int[] parents = new int[MAX_NODE + 1];		//	parents[v] : v 노드의 부모 노드 번호
	private static final int[] maxDepths = new int[MAX_NODE + 1];	//	maxDepths[v] : v 노드를 루트로 하는 서브트리의 최대 깊이 (루트노드의 깊이를 1로 가정)
	
	private static final int[] modified = new int[MAX_NODE + 1];	//	modified[v] : v 노드의 색깔이 결정된 시각
	
	private static int Q;		//	명령어 개수
	private static int time;	//	전역 시간
	
	public static void main(String[] args) throws IOException {
		for(int v = 0; v <= MAX_NODE; v++) {
			tree[v] = new ArrayList<>();
			parents[v] = NONE;
		}
		
		Q = Integer.parseInt(br.readLine());
		
		for(time = 1; time <= Q; time++) {
			st = new StringTokenizer(br.readLine());
			int opt = Integer.parseInt(st.nextToken());
			
			switch(opt) {
			case 100:
				addNode();
				break;
			case 200:
				changeColor();
				break;
			case 300:
				sb.append(getColor()).append("\n");
				break;
			case 400:
				sb.append(dfs()).append("\n");
				break;
			}
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static void addNode() {
		int mId = Integer.parseInt(st.nextToken());			//	추가할 노드 번호
		int pId = Integer.parseInt(st.nextToken());			//	해당 노드의 부모 노드 번호
		int color = Integer.parseInt(st.nextToken());		//	해당 노드의 색깔
		int maxDepth = Integer.parseInt(st.nextToken());	//	해당 노드를 루트로 하는 서브트리의 최대 깊이 
		
		if(pId == NONE) {	//	부모 노드가 없을 경우, mId 노드가 루트 노드가 됨
			rootList.add(mId);
			parents[mId] = pId;
			colors[mId] = color;
			maxDepths[mId] = maxDepth;
			modified[mId] = time;
		}
		else {	//	부모 노드가 존재할 경우
			int diff = 1;
			int cur = pId;
			
			while(cur != NONE) {
				int maxCurDepth = maxDepths[cur];
				
				if(maxCurDepth <= diff)
					return;
				
				diff++;
				cur = parents[cur];
			}
			
			parents[mId] = pId;
			colors[mId] = color;
			maxDepths[mId] = maxDepth;
			modified[mId] = time;
			
			tree[pId].add(mId);
		}
	}
	
	//	mId 노드를 루트로 하는 서브트리에 속한 모든 노드의 색깔을 color로 변경
	private static void changeColor() {
		int mId = Integer.parseInt(st.nextToken());			//	색깔을 변경할 노드 번호
		int color = Integer.parseInt(st.nextToken());		//	변경할 색깔
		
		colors[mId] = color;
		modified[mId] = time;
	}
	
	//	mId 노드의 색깔을 조회, mId 노드부터 mId가 포함된 트리의 루트까지 최대 100번 거슬러 올라가게 됨
	private static int getColor() {
		int mId = Integer.parseInt(st.nextToken());			//	색깔을 찾을 노드 번호
	
		int modTime = modified[mId];	//	mId 노드의 색깔이 마지막으로 결정된 시각
		int res = colors[mId];			//	최종 mId의 색깔
		
		int cur = parents[mId];			//	mId 노드의 조상 노드
		
		while(cur != NONE) {	//	조상 노드들을 거슬러 루트 노드까지 올라감
			if(modified[cur] > modTime) {	//	조상 노드의 색깔이 마지막으로 결정된 시각이 더 최신일 경우 
				res = colors[cur];			//	최종 mId의 색깔 변경
				modTime = modified[cur];
			}
			
			cur = parents[cur];
		}
		
		return res;
	}
	
	//	400 연산 하기 전에 모든 노드들의 색깔을 확정 짓기
	private static void setAllColors() {
		for(int root : rootList)
			setAllColors(root);
	}
	
	private static void setAllColors(int vertex) {
		int parent = parents[vertex];
		
		if(parent != NONE) {	//	부모가 존재할 경우
			if(modified[parent] > modified[vertex]) {	//	부모의 색깔이 더 최근에 변경된 경우
				colors[vertex] = colors[parent];
				modified[vertex] = modified[parent];
			}
		}
		
		for(int child : tree[vertex])
			setAllColors(child);
	}
	
	private static int dfs() {
		setAllColors();	//	점수 계산 전에 모든 노드의 색깔 확정 지어서 O(1)에 찾을 수 있게 하기
		
		int res = 0;
		int[] bitStatus = new int[MAX_NODE + 1];
		
		for(int root : rootList)
			res += dfs(root, bitStatus);
		
		return res;
	}
	
	private static int dfs(int vertex, int[] bitStatus) {
		int res = 0;
		int mask = 0 | (1 << colors[vertex] - 1);	//	vertex의 색깔만 고려했을때 비트 상태 
		
		for(int child : tree[vertex]) {
			res += dfs(child, bitStatus);
			mask |= bitStatus[child];
		}
		
		int cnt = cnt(mask);	//	자식 노드들까지 전부 고려했을때 vertex를 루트로 하는 서브트리에 포함된 색깔 개수
		res += cnt * cnt;
		
		bitStatus[vertex] = mask;
		
		return res;
	}
	
	private static int cnt(int mask) {
		int res = 0;
		
		String bits = Integer.toBinaryString(mask);
		for(int i = 0; i < bits.length(); i++) {
			if(bits.charAt(i) == '1')
				res++;
		}
		
		return res;
	}
}	//	Main-class-end
```

### 프로그래머스 **우유와 요거트가 담긴 장바구니**

[](https://school.programmers.co.kr/learn/courses/30/lessons/62284)

```sql
-- 코드를 입력하세요
select t.cart_id
from (
    select 
        cart_id, 
        (
            case 
                when name = 'Yogurt'
                then 1
                when name = 'Milk'
                then 2
                else 0
             end
        ) flag
    from cart_products
) t
group by t.cart_id
having sum(distinct t.flag) = 3
order by t.cart_id;
```