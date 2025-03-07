# 25_03_07_daily_certification

# Problem Solving (Algorithm & SQL)

### 프로그래머스 표 병합

[](https://school.programmers.co.kr/learn/courses/30/lessons/150366)

```java
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;

class Solution {
    private final String EMPTY = null;
    private final int SIZE = 50;
    
    private final int[] parents = new int[SIZE * SIZE];
    private final String[] values = new String[SIZE * SIZE];
    
    private final List<String> answer = new ArrayList<>();
    
    private StringTokenizer st;
    
    public String[] solution(String[] commands) {
        for(int v = 0; v < SIZE * SIZE; v++)
            parents[v] = v;
        
        for(String cmd : commands) {
            st = new StringTokenizer(cmd);
            String query = st.nextToken();
            
            if(query.equals("UPDATE")) {
                if(st.countTokens() == 3) {
                    int r = Integer.parseInt(st.nextToken()) - 1;
                    int c = Integer.parseInt(st.nextToken()) - 1;
                    String value = st.nextToken();
                    
                    update(r, c, value);
                }
                else {
                    String value1 = st.nextToken();
                    String value2 = st.nextToken();
                    
                    update(value1, value2);
                } 
            }
            else if(query.equals("MERGE")) {   
                int r1 = Integer.parseInt(st.nextToken()) - 1;
                int c1 = Integer.parseInt(st.nextToken()) - 1;
                int r2 = Integer.parseInt(st.nextToken()) - 1;
                int c2 = Integer.parseInt(st.nextToken()) - 1;
                
                merge(r1, c1, r2, c2);
            }
            else if(query.equals("UNMERGE")) {
                int r = Integer.parseInt(st.nextToken()) - 1;
                int c = Integer.parseInt(st.nextToken()) - 1;
                
                unmerge(r, c);
            }
            else {
                int r = Integer.parseInt(st.nextToken()) - 1;
                int c = Integer.parseInt(st.nextToken()) - 1;
                
                print(r, c);
            }
        }
        
        return answer.toArray(new String[0]);
    }
    
    private void update(int r, int c, String value) {
        int v = SIZE * r + c;
        values[find(v)] = value;
    }
    
    private void update(String value1, String value2) {
        for(int v = 0; v < SIZE * SIZE; v++) {
            int ds = find(v);
            if(values[ds] != null && values[ds].equals(value1))
                values[ds] = value2;
        }
    }
    
    private void merge(int r1, int c1, int r2, int c2) {
        int v1 = SIZE * r1 + c1;
        int v2 = SIZE * r2 + c2;

        union(v1, v2);
    }
    
    private void unmerge(int r, int c) {
        int pos = SIZE * r + c;
        int merged = find(pos);
        String value = values[merged];
        
        for(int v = 0; v < SIZE * SIZE; v++)
            find(v);

        for(int v = 0; v < SIZE * SIZE; v++) {
            if(parents[v] == merged) {
                parents[v] = v;
                if(v == pos)
                    values[v] = value;
                else
                    values[v] = null;
            }
        }
    }
    
    private void print(int r, int c) {
        int ds = find(SIZE * r + c);
                
        answer.add(values[ds] == null ? "EMPTY" : values[ds]);
    }
    
    private int find(int v) {
        if(v == parents[v])
            return v;
        
        return parents[v] = find(parents[v]);
    }
    
    private void union(int v1, int v2) {
        int v1Ds = find(v1);
        int v2Ds = find(v2);

        if(v1Ds != v2Ds) {  //  같은 셀이 아닐 경우
            if(values[v1Ds] != null)
                parents[v2Ds] = v1Ds;
            else
                parents[v1Ds] = v2Ds;
        }
    }
}
```

### 프로그래머스 업그레이드 할 수 없는 아이템 구하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/273712)

```sql
-- 코드를 작성해주세요
select t1.item_id, t1.item_name, t1.rarity
from item_info t1 left join item_tree t2
on t1.item_id = t2.parent_item_id
where t2.parent_item_id is null
order by t1.item_id desc;
```

### 프로그래머스 오랜 기간 보호한 동물(2)

[](https://school.programmers.co.kr/learn/courses/30/lessons/59411)

```sql
-- 코드를 입력하세요
select t1.animal_id, t1.name
from animal_ins t1 inner join animal_outs t2
where t1.animal_id = t2.animal_id
order by t2.datetime - t1.datetime desc
limit 2;
```