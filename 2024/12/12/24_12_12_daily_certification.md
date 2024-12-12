# 24_12_12_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 3425** **고스택**

[3425번: 고스택](https://boj.ma/3425/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.Stack;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX = 1_000_000_000;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static final Stack<Integer> stack = new Stack<>();
    private static final List<String> program = new ArrayList<>();
    private static final List<Integer> input = new ArrayList<>();
    private static boolean programEnd = false;
    private static int N = -1;	//	입력 영역 개수
    
    public static void main(String[] args) throws IOException {  
        while(true) {
        	String cmd = br.readLine();
        	
        	if(cmd.equals("QUIT")) {	//	다음 기계가 없을 경우
        		System.out.print(sb);
        		break;
        	}
        	else if(cmd.equals("")) {	//	이번 기계 설명이 끝났을 경우, 기계 실행 후 초기화
        		run();
        		program.clear();
        		input.clear();
        		programEnd = false;
        		N = -1;
        		continue;
        	}
        	
        	if(!programEnd) {	//	program 입력일 경우
        		if(cmd.equals("END"))	//	프로그램 입력이 종료되었을 경우
        			programEnd = true;
        		else
        			program.add(cmd);
        	}
        	else {	//	input 입력일 경우
        		if(N == -1)
        			N = Integer.parseInt(cmd);
        		else
        			input.add(Integer.parseInt(cmd));
        	}
        }
    }    //    main-end
    
    private static void run() {
    	for(int i = 0; i < N; i++) {
    		stack.push(input.get(i));
    		
    		boolean error = false;
    		
    		for(String cmd : program) {
    			st = new StringTokenizer(cmd);
    			String opt = st.nextToken();
    			
    			if(opt.equals("NUM")) {
    				int x = Integer.parseInt(st.nextToken());
    				stack.push(x);
    			}
    			else if(opt.equals("POP")) {
    				if(stack.isEmpty()) {
    					error = true;
    					break;
    				}
    				
    				stack.pop();
    			}
    			else if(opt.equals("INV")) {
    				if(stack.isEmpty()) {
    					error = true;
    					break;
    				}
    				
    				int pop = stack.pop();
    				stack.push(-pop);
    			}
    			else if(opt.equals("DUP")) {
    				if(stack.isEmpty()) {
    					error = true;
    					break;
    				}
    				
    				stack.push(stack.peek());
    			}
    			else if(opt.equals("SWP")) {
    				if(stack.size() < 2) {
    					error = true;
    					break;
    				}
    				
    				int pop1 = stack.pop();
    				int pop2 = stack.pop();
    				stack.push(pop1);
    				stack.push(pop2);
    			}
    			else if(opt.equals("ADD")) {
    				if(stack.size() < 2) {
    					error = true;
    					break;
    				}
    				
    				int pop1 = stack.pop();
    				int pop2 = stack.pop();
    				
    				if(Math.abs(pop1 + pop2) > MAX) {
    					error = true;
    					break;
    				}
    				
    				stack.push(pop1 + pop2);
    			}
    			else if(opt.equals("SUB")) {
    				if(stack.size() < 2) {
    					error = true;
    					break;
    				}
    				
    				int pop1 = stack.pop();
    				int pop2 = stack.pop();
    				
    				if(Math.abs(pop2 - pop1) > MAX) {
    					error = true;
    					break;
    				}
    				
    				stack.push(pop2 - pop1);
    			}
    			else if(opt.equals("MUL")) {
    				if(stack.size() < 2) {
    					error = true;
    					break;
    				}
    				
    				int pop1 = stack.pop();
    				int pop2 = stack.pop();
    				
    				if(Math.abs((long)pop1 * pop2) > MAX) {
    					error = true;
    					break;
    				}
    				
    				stack.push(pop1 * pop2);
    			}
    			else if(opt.equals("DIV")) {
    				if(stack.size() < 2) {
    					error = true;
    					break;
    				}
    				
    				int pop1 = stack.pop();
    				int pop2 = stack.pop();
    				
    				if(pop1 == 0) {
    					error = true;
    					break;
    				}
    				
    				stack.push(pop2 / pop1);
    			}
    			else {
    				if(stack.size() < 2) {
    					error = true;
    					break;
    				}
    				
    				int pop1 = stack.pop();
    				int pop2 = stack.pop();
    				
    				if(pop1 == 0) {
    					error = true;
    					break;
    				}
    				
    				stack.push(pop2 % pop1);
    			}
    		}
    		
    		if(stack.size() != 1)
    			error = true;
    		
    		sb.append(error ? "ERROR" : stack.pop()).append("\n");
    		stack.clear();
    	}
    	
    	sb.append("\n");
    }
}    //    Main-class-end
```

### LeetCode **Single Number**

[Single Number - LeetCode](https://leetcode.com/problems/single-number/)

```java
class Solution {
    public int singleNumber(int[] nums) {
        int mask = 0;

        for(int i = 0; i < nums.length; i++)
            mask = mask ^ nums[i];

        return mask;
    }
}
```

### LeetCode **Minimum Distance Between BST Nodes**

[Minimum Distance Between BST Nodes - LeetCode](https://leetcode.com/problems/minimum-distance-between-bst-nodes/description/)

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    private int minDiff = Integer.MAX_VALUE;

    public int minDiffInBST(TreeNode root) {
        dfs(root);

        return minDiff;    
    }

    //  root subtree의 노드 중 {최솟값, 최댓값 반환}
    private int[] dfs(TreeNode root) {
        if(root == null)
            return null;

        int[] left = dfs(root.left);
        int[] right = dfs(root.right);
        int[] res = new int[2];

        if(left == null)    //  root보다 작은 노드가 없을 경우
            res[0] = root.val;  //  root 서브트리의 최솟값은 root.val
        else {
            res[0] = left[0];   //  root 서브트리의 최솟값은 root.left의 최솟값
            minDiff = Math.min(minDiff, root.val - left[1]); //  root의 값과 root.left의 값 중 최댓값과의 차이
        }

        if(right == null)   //  root보다 큰 노드가 없을 경우
            res[1] = root.val;  //  root  서브트리의 최댓값은 root.val
        else {
            res[1] = right[1];  //  root 서브트리의 최댓값은 root.right의 최댓값
            minDiff = Math.min(minDiff, right[0] - root.val);   //  root.right의 값 중 최솟값과 root의 값의 차이
        }

        return res;
    }
}
```

### LeetCode Course Schedule

[Course Schedule - LeetCode](https://leetcode.com/problems/course-schedule/description/)

```java
class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        List<Integer>[] graph = new ArrayList[numCourses];
        for(int v = 0; v < numCourses; v++)
            graph[v] = new ArrayList<>();

        int[] indegrees = new int[numCourses];
        int cnt = 0;

        for(int i = 0; i < prerequisites.length; i++) {
            int next = prerequisites[i][0];
            int prev = prerequisites[i][1];

            graph[prev].add(next);
            indegrees[next]++;
        }

        Queue<Integer> queue = new LinkedList<>();
        for(int v = 0; v < numCourses; v++) {
            if(indegrees[v] == 0) {
                cnt++;
                queue.offer(v);
            }
        }

        while(!queue.isEmpty()) {
            int prev = queue.poll();

            for(int next : graph[prev]) {
                indegrees[next]--;
                if(indegrees[next] == 0) {
                    queue.offer(next);
                    cnt++;
                }
            }
        }

        return cnt == numCourses;
    }
}
```

### LeetCode **Most Common Word**

[Most Common Word - LeetCode](https://leetcode.com/problems/most-common-word/description/)

```java
import java.util.StringTokenizer;

class Solution {
    public String mostCommonWord(String paragraph, String[] banned) {
        String mcw = "";
        int mcwCnt = 0;

        Set<String> bannedSet = new HashSet<>();
        Map<String, Integer> map = new HashMap<>();

        for(String word : banned)
            bannedSet.add(word);

        StringTokenizer st = new StringTokenizer(paragraph, " |!|?|'|,|;|.");

        while(st.hasMoreTokens()) {
            String word = st.nextToken().toLowerCase();
            
            if(!bannedSet.contains(word)) {
                int cnt = map.getOrDefault(word, 0) + 1;

                if(cnt > mcwCnt) {
                    mcwCnt = cnt;
                    mcw = word;
                }

                map.put(word, cnt);
            } 
        }

        return mcw;
    }
}
```

### LeetCode **Merge Two Binary Trees**

[Merge Two Binary Trees - LeetCode](https://leetcode.com/problems/merge-two-binary-trees/description/)

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public TreeNode mergeTrees(TreeNode root1, TreeNode root2) {
        return dfs(root1, root2);
    }

    private TreeNode dfs(TreeNode root1, TreeNode root2) {
        if(root1 == null && root2 == null)
            return null;
        else if(root1 == null) 
            return root2;
        else if(root2 == null)
            return root1;  
        else {
            TreeNode newLeft = dfs(root1.left, root2.left);
            TreeNode newRight = dfs(root1.right, root2.right);
            TreeNode newRoot = new TreeNode(root1.val + root2.val);

            newRoot.left = newLeft;
            newRoot.right = newRight;

            return newRoot;
        }          
    }
}
```