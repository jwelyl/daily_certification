# 25_09_29_daily_certification

```
[#272 koreii] 데일리인증 20250929
0. 10시간 채우기 (0/10)
1. 취업
- 서류 1 작성 중
- 면접 준비
2. 알고리즘 문제 풀이
- BOJ 2461 대표 선수 (Sorting, Two Pointer)
- BOJ 24511 queuestack (Stack, Queue)
```

```
[#90] 데일리인증 20250928 (271 / 365)
0. 8시간 채우기 (8/8)
1. 취업
- 서류 1 작성 중
- 면접 준비
2. 알고리즘 문제 풀이
- BOJ 2461 대표 선수 (Sorting, Two Pointer)
- BOJ 24511 queuestack (Stack, Queue)
```

# Problem Solving (Algorithm & SQL)

### BOJ 2461 대표 선수

[2461번: 대표 선수](http://boj.ma/2461/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Collections;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    학급 수
    private static int M;    //    학급별 학생 수
    
    //    학생 리스트
    private static final List<Student> students = new ArrayList<>();
    //    idArr[id] : [start, end] 구간에 있는 학급 번호가 id인 학생 수
    private static int[] idArr;
    //    idCnt : [start, end] 구간에 있는 학급 번호 종류의 수
    private static int idCnt = 0;
    
    private static int start = 0;
    private static int end = 0;
    
    private static int answer = Integer.MAX_VALUE;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        idArr = new int[N];
        for(int id = 0; id < N; id++) {
            st = new StringTokenizer(br.readLine());
            for(int i = 0; i < M; i++)
                students.add(new Student(Integer.parseInt(st.nextToken()), id));
        }
        
        Collections.sort(students);
        end = N - 1;    //    N개 학급이 모두 포함되기 위해서 [start, end] 길이는 최소 N이어야 함
        
        for(int i = start; i <= end; i++) {
            Student student = students.get(i);
            int id = student.id;
            
            idArr[id]++;          //    [start, end] 구간에 포함된 id 개수 1 증가
            if(idArr[id] == 1)    //    [start, end] 구간에 id가 새로 생긴 경우
                idCnt++;          //    [start, end] 구간의 학급 번호 종류 수 1 증가
        }
        
        if(idCnt == N)    //    최초 [start, end] 구간에 학급 번호가 0 ~ N - 1까지 모두 존재할 경우
            answer = students.get(end).cap - students.get(start).cap;
        
        while(end < students.size()) {
            if(idCnt < N) {    //    [start, end] 구간에 포함된 학급 번호 종류가 N개 미만일 경우
                if(end + 1 < students.size()) {
                    Student next = students.get(end + 1);
                    int id = next.id;
                    idArr[id]++;
                    if(idArr[id] == 1)
                        idCnt++;
                }
                end++;    //    end 늘려서 [start, end]에 포함된 학급 번호 종류 증가시켜야 함
            }
            else {    //    [start, end] 구간에 포함된 학급 번호 종류가 N개일 경우
                Student front = students.get(start);
                Student rear = students.get(end);
                
                answer = Math.min(answer, rear.cap - front.cap);
                
                idArr[front.id]--;
                if(idArr[front.id] == 0)
                    idCnt--;
                
                start++;
            }
        }
        
        System.out.println(answer);
    }    //    main-end
    
    private static class Student implements Comparable<Student> {
        public int cap;        //    능력치
        public int id;         //    학급 번호
        
        public Student(int cap, int id) {
            this.cap = cap;
            this.id = id;
        }
        
        @Override
        public int compareTo(Student other) {
            //    능력치 순으로 정렬
            return Integer.compare(this.cap, other.cap);
        }
    }
}    //    Main-class-end
```

### BOJ 24511 queuestack

[24511번: queuestack](http://boj.ma/24511/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.ArrayDeque;

public class Main {
    private static final int Q = 0;    //    큐
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;         //    큐 + 스택 개수
    private static int[] sort;    //    자료구조 종류 큐/스택
    private static int[] init;    //    초기에 자료구조에 저장된 값
    
    private static int M;         //    입력 원소 수
    
    private static final Queue<Integer> queueStack = new ArrayDeque<>();
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        sort = new int[N];
        init = new int[N];
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++)
            sort[i] = Integer.parseInt(st.nextToken());
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++)
            init[i] = Integer.parseInt(st.nextToken());
        
        for(int i = N - 1; i >= 0; i--) {
            if(sort[i] == Q)
                queueStack.offer(init[i]);
        }
        
        M = Integer.parseInt(br.readLine());
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < M; i++)
            queueStack.offer(Integer.parseInt(st.nextToken()));
        
        while(M-- > 0)
            sb.append(queueStack.poll()).append(" ");
        
        System.out.println(sb);
    }    //    main-end
}    //    Main-class-end
```