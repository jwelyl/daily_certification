# 25_07_13_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2505 두 번 뒤집기

[2505번: 두 번 뒤집기](http://boj.ma/2505/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
 
public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
    private static int[] flipped;
    
    private static int N;
    private static int from1 = 1;
    private static int to1 = 1;
    private static int from2 = 1;
    private static int to2 = 1;
    
    private static boolean find = false;
     
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        flipped = new int[N + 1];
         
        st = new StringTokenizer(br.readLine());
        for (int i = 1; i <= N; i++)
            flipped[i] = Integer.parseInt(st.nextToken());
         

        //	앞에서부터 뒤집을 구간 찾기
        front();
        
        if(!find)	//	앞에서부터 뒤집을 구간 찾았을때 못찾을 경우
        	back();	//	뒤에서부터 뒤집을 구간 찾기
        
        System.out.println(from1 + " " + to1);
        System.out.println(from2 + " " + to2);     
    }	//	main-end
 
    private static void front() {
        int[] arr = flipped.clone();
        int cnt = 0;	//	뒤집는 횟수
        
        int idx = 1;
        
        while(idx <= N) {
        	if(arr[idx] != idx) {	//	뒤집어야 하는 구간 발견
        		int from = idx;
        		int to = N;
        		
        		for(int i = idx + 1; i <= N; i++) {
        			if(arr[i] == idx) {
        				to = i;
        				break;
        			}
        		}
        		
        		reverse(arr, from, to);
                cnt++;
        		
                if(cnt == 1) {	//	뒤집어야 하는 구간이 하나일 경우
                	from1 = from;
                	to1 = to;
                }
                else if(cnt == 2) {	//	뒤집어야 하는 구간이 두개일 경우
                	from2 = from;
                	to2 = to;
                }
        	}
        	if(cnt == 3) 	//	뒤집어야 하는 구간이 3개 이상일 경우
        		return;
        	
        	idx++;
        }
        
        find = true;
    }
    
    private static void back() {
        int[] arr = flipped.clone();
        int cnt = 0;	//	뒤집는 횟수
        
        int idx = N;
        
        while(idx >= 1) {
        	if(arr[idx] != idx) {	//	뒤집어야 하는 구간 발견
        		int to = idx;
        		int from = 1;
        		
        		for(int i = idx - 1; i >= 1; i--) {
        			if(arr[i] == idx) {
        				from = i;
        				break;
        			}
        		}
        		
        		reverse(arr, from, to);
                cnt++;
        		
                if(cnt == 1) {	//	뒤집어야 하는 구간이 하나일 경우
                	from1 = from;
                	to1 = to;
                }
                else if(cnt == 2) {	//	뒤집어야 하는 구간이 두개일 경우
                	from2 = from;
                	to2 = to;
                }
        	}
        	if(cnt == 3) 	//	뒤집어야 하는 구간이 3개 이상일 경우
        		return;
        	
        	idx--;
        }
        
        find = true;
    }
   
    private static void reverse(int[] arr, int from, int to) {
        while(from < to) {
            int tmp = arr[from];
            arr[from] = arr[to];
            arr[to] = tmp;
            from++; 
            to--;
        }
    }
}	//	Main-class-end
```