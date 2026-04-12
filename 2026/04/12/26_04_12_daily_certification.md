# 26_04_12_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1079 마피아

[1079번: 마피아](http://boj.ma/1079/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;                //    참가자 수
    private static int[] guilties;       //    유죄 지수
    private static int M;                //    마피아 번호

    //    밤에 마피아에 의해 참가자 i가 죽으면 다른 사람 j의 유죄 지수가 relations[i][j]만큼 증가함
    private static int[][] relations;
    
    //    isDead[i] : i번째 참가자가 죽었으면 true
    private static boolean[] isDead;
    
    private static int answer = 0;    //    최대 밤 수
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        guilties = new int[N];
        relations = new int[N][N];
        isDead = new boolean[N];
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++)
            guilties[i] = Integer.parseInt(st.nextToken());
        for(int i = 0; i < N; i++) {
            st = new StringTokenizer(br.readLine());
            for(int j = 0; j < N; j++)
                relations[i][j] = Integer.parseInt(st.nextToken());
        }
        
        M = Integer.parseInt(br.readLine());
        
        backtracking(N - 1, 1, 0);
        
        System.out.println(answer);
    }    //    main-end
    
    private static void backtracking(int citizen, int mafia, int night) {
        if(citizen == 0 || mafia == 0) {    //    시민과 마피아 둘 중 하나가 전멸했을 경우, 게임 종료
            answer = Math.max(answer, night);
            return;
        }
        
        if((citizen + mafia) % 2 == 0) {      //    남은 참가자 수가 짝수일 경우, 밤임
            for(int i = 0; i < N; i++) {      //    마피아가 죽일 시민을 찾기    
                if(i != M && !isDead[i]) {    //    마피아가 아니고, 아직 살아있는 시민
                    isDead[i] = true;         //    i번째 시민 죽이기
                    for(int j = 0; j < N; j++)            //    i번째 시민이 죽어서 다른 참가자의 유죄 지수 변화
                        guilties[j] += relations[i][j];
                    
                    backtracking(citizen - 1, mafia, night + 1);
                    
                    isDead[i] = false;        //    원상복구
                    for(int j = 0; j < N; j++)
                        guilties[j] -= relations[i][j];
                }
            }
        }
        else {    //    남은 참가자 수가 홀수일 경우, 낮임
            int maxGuilty = Integer.MIN_VALUE;
            int target = -1;    //    재판으로 죽일 사람
            
            for(int i = 0; i < N; i++) {    //    가장 유죄 지수 높은 사람 찾음
                if(!isDead[i] && guilties[i] > maxGuilty) {
                    maxGuilty = guilties[i];
                    target = i;
                }
            }
            
            isDead[target] = true;
            if(target == M)    //    마피아를 잡은 경우
                backtracking(citizen, mafia - 1, night);
            else               //    시민을 잡은 경우
                backtracking(citizen - 1, mafia, night);
            isDead[target] = false;
        }
    }
}    //    Main-class-end
```