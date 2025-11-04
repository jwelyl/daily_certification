# 25_11_04_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 4716 풍선

[4716번: 풍선](http://boj.ma/4716/t)

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

    private static int N;	//	팀 개수
    private static int A;	//	A 방에 있는 풍선 개수
    private static int B;	//	B 방에 있는 풍선 개수
    
    private static final List<Team> teamList = new ArrayList<>();
    
    private static long answer = 0;

    public static void main(String[] args) throws IOException {
        while (true) {
            st = new StringTokenizer(br.readLine());
            N = Integer.parseInt(st.nextToken());
            A = Integer.parseInt(st.nextToken());
            B = Integer.parseInt(st.nextToken());

            if (N == 0 && A == 0 && B == 0) {
                System.out.print(sb);
                break;
            }

            teamList.clear();
            
            for (int i = 0; i < N; i++) {
                st = new StringTokenizer(br.readLine());
                int required = Integer.parseInt(st.nextToken());
                int distA = Integer.parseInt(st.nextToken());
                int distB = Integer.parseInt(st.nextToken());
                teamList.add(new Team(required, distA, distB));
            }

            teamList.sort((team1, team2) -> Integer.compare(Math.abs(team2.diff), Math.abs(team1.diff)));

            answer = 0;

            for (Team tean : teamList) {
                int use;
                if (tean.distA < tean.distB) { // A가 더 유리
                    use = Math.min(A, tean.required);
                    answer += (long) use * tean.distA;
                    A -= use;
                    int remain = tean.required - use;
                    if (remain > 0) {
                        answer += (long) remain * tean.distB;
                        B -= remain;
                    }
                } else { // B가 더 유리
                    use = Math.min(B, tean.required);
                    answer += (long) use * tean.distB;
                    B -= use;
                    int remain = tean.required - use;
                    if (remain > 0) {
                        answer += (long) remain * tean.distA;
                        A -= remain;
                    }
                }
            }

            sb.append(answer).append("\n");
        }
    }	//	main-end

    private static class Team {
        public int required;	//	필요한 풍선 개수
        public int distA;			
        public int distB;		//	A, B까지의 거리
        public int diff; 		//  distA - distB

        public Team(int required, int distA, int distB) {
            this.required = required;
            this.distA = distA;
            this.distB = distB;
            this.diff = distA - distB;
        }
    }
}	//	Main-class-end
```