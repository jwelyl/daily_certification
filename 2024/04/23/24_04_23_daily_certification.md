# 24_04_23_daily_certification

# 데이터베이스

# Normalization

## Normalization (정규화)

데이터 중복과 insertion, update, deletion anomaly를 최소화하기 위해 **Normal Forms(NF)에 따라 Relataion DB를 구성하는 과정**

## Normal Forms (NF)

정규화되기 위해 준수해야 하는 rule들을 Normal Froms (NF)이라고 한다.

![Untitled](24_04_23_daily_certification%20075e2663cd3548308ea27d5e507c3511/Untitled.png)

초기 테이블부터 1NF부터 순차적으로 만족하도록 테이블 구조를 조정한다.

어떤 테이블이 3NF를 만족한다면 1NF, 2NF는 이미 만족한 상태이다.

**1NF부터 BCNF까지는 Functional Dependency와 Key만으로 정의된다.**

**3NF까지 만족하는 것이 정규화의 최소 기준이다. 보통 실무에서는 많아야 4NF까지 진행한다.**

다음 테이블로 정규화를 진행해보자.

**EMPLOYEE_ACCOUNT**

| bank_name | account_num | account_id | class | ratio | empl_id | empl_name | card_id |
| --- | --- | --- | --- | --- | --- | --- | --- |
- 임직원의 월급 계좌를 관리하는 테이블이다.
- 월급 계좌는 국민은행이나 우리은행 계좌 중 하나이다.
- 한 임직원이 하나 이상의 월급 계좌를 등록하고 월급 비율(ratio)을 조정할 수 있다.
- 계좌마다 등급(class)가 존재한다.
    - 국민 : STAR → PRESTIGE → LOYAL
    - 우리 : BRONZE → SILVER → GOLD
- 한 계좌는 하나 이상의 현금 카드와 연동될 수 있다.

**EMPLOYEE_ACCOUNT**

| bank_name | account_num | account_id | class | ratio | empl_id | empl_name | card_id |
| --- | --- | --- | --- | --- | --- | --- | --- |

위 테이블의 **Key (Candidate Key)**는 다음과 같다.

**{account_id} : 계좌 id로 계좌 정보를 유일하게 식별할 수 있다.**

**{bank_name, account_num} : 은행 이름과 계좌 번호를 통해 계좌 정보를 유일하게 식별할 수 있다.**

**Primary Key를 {account_id}로 하자.**

**EMPLOYEE_ACCOUNT**

| bank_name | account_num | account_id | class | ratio | empl_id | empl_name | card_id |
| --- | --- | --- | --- | --- | --- | --- | --- |

### Prime Attribute

**테이블에서 임의의 key에 속하는 attribute**

account_id, bank_name, account_num

### Non-prime Attribute

**어떤 key에도 속하지 않는 attribute**

class, ratio, empl_id, empl_name, card_id

### Functional Dependency

**EMPLOYEE_ACCOUNT**

| bank_name | account_num | account_id | class | ratio | empl_id | empl_name | card_id |
| --- | --- | --- | --- | --- | --- | --- | --- |

account_id는 PK이므로 아래와 같은 FD가 성립한다.

**{account_id} → {bank_name, account_num, class, ratio,  empl_id, empl_name, card_id}**

이를 그림으로 나타내면 다음과 같다.

![Untitled](24_04_23_daily_certification%20075e2663cd3548308ea27d5e507c3511/Untitled%201.png)

**EMPLOYEE_ACCOUNT** 에서는 다음과 같은 FD가 성립한다.

![Untitled](24_04_23_daily_certification%20075e2663cd3548308ea27d5e507c3511/Untitled%202.png)

**{account_id} → {bank_name, account_num, class, ratio,  empl_id, empl_name, card_id}**

**{bank_name, account_num} → {account_id, class, ratio,  empl_id, empl_name, card_id}**

**{empl_id} → {empl_name}**

**{class} → {bank_name}**

정규화 과정에서 데이터 중복이 사라지는 지 확인하기 위해 데이터를 넣고 정규화를 진행해본다.

**EMPLOYEE_ACCOUNT**

| bank_name | account_num | account_id | class | ratio | empl_id | empl_name | card_id |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Woori | 010-9231-1121 | a11 | BRONZE | 0.1 | e1 | Sony | c101 |
| Woori | 102-992-180125 | a11 | SILVER | 0.2 | e1 | Sony | c102 |
| Kookmin | 010-9231-1121 | a13 | LOYAL | 0.7 | e1 | Sony | c103 |
| Kookmin | 010-1221-1732 | a21 | LOYAL | 1 | e2 | Messi | c201
c202 |

## 1NF

**모든 Attribute의 value는 반드시 나눠질 수 없는 단일한 값이어야 한다.**

**EMPLOYEE_ACCOUNT**

| bank_name | account_num | account_id | class | ratio | empl_id | empl_name | card_id |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Woori | 010-9231-1121 | a11 | BRONZE | 0.1 | e1 | Sony | c101 |
| Woori | 102-992-180125 | a11 | SILVER | 0.2 | e1 | Sony | c102 |
| Kookmin | 010-9231-1121 | a13 | LOYAL | 0.7 | e1 | Sony | c103 |
| Kookmin | 010-1221-1732 | a21 | LOYAL | 1 | e2 | Messi | c201
c202 |

4번째 tuple의 card_id가 1NF를 위반한다.

**EMPLOYEE_ACCOUNT**

| bank_name | account_num | account_id | class | ratio | empl_id | empl_name | card_id |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Woori | 010-9231-1121 | a11 | BRONZE | 0.1 | e1 | Sony | c101 |
| Woori | 102-992-180125 | a11 | SILVER | 0.2 | e1 | Sony | c102 |
| Kookmin | 010-9231-1121 | a13 | LOYAL | 0.7 | e1 | Sony | c103 |
| Kookmin | 010-1221-1732 | a21 | LOYAL | 1 | e2 | Messi | c201 |
| Kookmin | 010-1221-1732 | a21 | LOYAL | 1 | e2 | Messi | c202 |

4번째 tuple을 두 개의 tuples로 나누어 해결하면 1NF를 만족한다.

하지만 중복되는 데이터가 생긴다.

Primary Key도 변경해줘야 한다. (account_id가 모든 tuple을 고유하게 식별할 수 없다.)

## 2NF

**모든 Non-prime Attribute들은 모든 Key에 대해 Fully Dependent해야 한다.**

**EMPLOYEE_ACCOUNT**

| bank_name | account_num | account_id | class | ratio | empl_id | empl_name | card_id |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Woori | 010-9231-1121 | a11 | BRONZE | 0.1 | e1 | Sony | c101 |
| Woori | 102-992-180125 | a11 | SILVER | 0.2 | e1 | Sony | c102 |
| Kookmin | 010-9231-1121 | a13 | LOYAL | 0.7 | e1 | Sony | c103 |
| Kookmin | 010-1221-1732 | a21 | LOYAL | 1 | e2 | Messi | c201
c202 |

1NF로 변경 전 원래 테이블의 Key는 다음 2가지가 있었다.

**{account_id}, {bank_name, account_num}**

**EMPLOYEE_ACCOUNT**

| bank_name | account_num | account_id | class | ratio | empl_id | empl_name | card_id |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Woori | 010-9231-1121 | a11 | BRONZE | 0.1 | e1 | Sony | c101 |
| Woori | 102-992-180125 | a11 | SILVER | 0.2 | e1 | Sony | c102 |
| Kookmin | 010-9231-1121 | a13 | LOYAL | 0.7 | e1 | Sony | c103 |
| Kookmin | 010-1221-1732 | a21 | LOYAL | 1 | e2 | Messi | c201 |
| Kookmin | 010-1221-1732 | a21 | LOYAL | 1 | e2 | Messi | c202 |

1NF로 변경 후 Key는 다음과 같이 변경된다.

**{account_id, card_id}, {bank_name, account_num, card_id}**

Non-prime Attribute는 class, ratio, empl_id, empl_name이다.

![Untitled](24_04_23_daily_certification%20075e2663cd3548308ea27d5e507c3511/Untitled%203.png)

**{account_id, card_id} → {bank_name, account_num, class, ratio, empl_id, empl_name}**

그런데 Non-prime Attribute인 class, ratio, empl_id, empl_name에 대해서는 다음도 성립한다.

![Untitled](24_04_23_daily_certification%20075e2663cd3548308ea27d5e507c3511/Untitled%204.png)

**{account_id} → {class, ratio, empl_id, empl_name}**

즉, 모든 Non-prime Attribute들이 Key {account_id, card_id}에 대해 partially dependent하다.

![Untitled](24_04_23_daily_certification%20075e2663cd3548308ea27d5e507c3511/Untitled%205.png)

**{bank_name, account_num} → {class, ratio, empl_id, empl_name}**

즉, 모든 Non-prime Attribute들이 Key {bank_name, account_num, card_id}에 대해 partially dependent하다.

card_id가 Key에 포함되면서 위와 같은 문제가 발생했다. **따라서 card_id를 분리해야 한다.**

ACCOUNT_CARD 테이블을 분리한다. 이 때, EMPLOYEE_ACCOUNT와 join이 가능하도록 card_id와 account_id를 같이 ACCOUNT_CARD 테이블에 넣어준다.

**EMPLOYEE_ACCOUNT**

| bank_name | account_num | account_id | class | ratio | empl_id | empl_name |
| --- | --- | --- | --- | --- | --- | --- |
| Woori | 010-9231-1121 | a11 | BRONZE | 0.1 | e1 | Sony |
| Woori | 102-992-180125 | a11 | SILVER | 0.2 | e1 | Sony |
| Kookmin | 010-9231-1121 | a13 | LOYAL | 0.7 | e1 | Sony |
| Kookmin | 010-1221-1732 | a21 | LOYAL | 1 | e2 | Messi |

**ACCOUNT_CARD**

| account_id | card_id |
| --- | --- |
| a11 | c101 |
| a11 | c102 |
| a13 | c103 |
| a21 | c201 |
| a21 | c202 |

분리된 테이블은 2NF를 만족하는 지 확인해야 한다.

**EMPLOYEE_ACCOUNT**

| bank_name | account_num | account_id | class | ratio | empl_id | empl_name |
| --- | --- | --- | --- | --- | --- | --- |
| Woori | 010-9231-1121 | a11 | BRONZE | 0.1 | e1 | Sony |
| Woori | 102-992-180125 | a11 | SILVER | 0.2 | e1 | Sony |
| Kookmin | 010-9231-1121 | a13 | LOYAL | 0.7 | e1 | Sony |
| Kookmin | 010-1221-1732 | a21 | LOYAL | 1 | e2 | Messi |

{account_id} → {class, ratio, empl_id, empl_name} 을 만족하고,

{bank_name, account_num} → {class, ratio, empl_id, empl_name}를 만족하므로

2NF를 만족한다.

## 3NF

**모든 Non-prime Attribute는 어떤 Key에도 Transitively Dependent하면 안된다.**

**Non-prime Attribute와 Non-prime Attribute 사이에는 FD가 존재해서는 안된다.**

2NF를 만족하는 테이블에서

**EMPLOYEE_ACCOUNT**

| bank_name | account_num | account_id | class | ratio | empl_id | empl_name |
| --- | --- | --- | --- | --- | --- | --- |
| Woori | 010-9231-1121 | a11 | BRONZE | 0.1 | e1 | Sony |
| Woori | 102-992-180125 | a11 | SILVER | 0.2 | e1 | Sony |
| Kookmin | 010-9231-1121 | a13 | LOYAL | 0.7 | e1 | Sony |
| Kookmin | 010-1221-1732 | a21 | LOYAL | 1 | e2 | Messi |

**ACCOUNT_CARD**

| account_id | card_id |
| --- | --- |
| a11 | c101 |
| a11 | c102 |
| a13 | c103 |
| a21 | c201 |
| a21 | c202 |

**EMPLOYEE_ACCOUNT**

| bank_name | account_num | account_id | class | ratio | empl_id | empl_name |
| --- | --- | --- | --- | --- | --- | --- |
| Woori | 010-9231-1121 | a11 | BRONZE | 0.1 | e1 | Sony |
| Woori | 102-992-180125 | a11 | SILVER | 0.2 | e1 | Sony |
| Kookmin | 010-9231-1121 | a13 | LOYAL | 0.7 | e1 | Sony |
| Kookmin | 010-1221-1732 | a21 | LOYAL | 1 | e2 | Messi |

데이터 중복이 보인다. 왜 그럴까?

![Untitled](24_04_23_daily_certification%20075e2663cd3548308ea27d5e507c3511/Untitled%206.png)

{account_id} → {empl_id}, {empl_id} → {empl_name} 가 존재한다.

**위 두 FD를 합쳐서 {account_id} → {empl_name}으로 표시할 수 있다.**

{bank_name, account_num} → {empl_id}, {empl_id} → {empl_name} 가 존재한다.

**위 두 FD를 합쳐서 {bank_name, account_num} → {empl_name}으로 표시할 수 있다.**

### Transitive Functional Dependency

![Untitled](24_04_23_daily_certification%20075e2663cd3548308ea27d5e507c3511/Untitled%207.png)

**X → Y 이고 Y → Z 이며, Y, Z가 Key의 부분 집합이 아닐 경우 X → Z 가 성립한다.**

{account_id} → {class}이고 {class} → {bank_name}이지만 bank_name이 Key에 포함되므로 {account_id} → {bank_name}은 성립하지 않는다.

**empl_name은 두 Key {account_id}, {bank_name, account_num}에 대해 Transitively Dependent하므로 3NF를 위반한다.**

**{empl_id} → {empl_name} 이므로 Non-prime Attribute 사이에 FD가 존재하므로 3NF를 위반한다.**

이를 제거해야 한다.

**EMPLOYEE_ACCOUNT**

| bank_name | account_num | account_id | class | ratio | empl_id |
| --- | --- | --- | --- | --- | --- |
| Woori | 010-9231-1121 | a11 | BRONZE | 0.1 | e1 |
| Woori | 102-992-180125 | a11 | SILVER | 0.2 | e1 |
| Kookmin | 010-9231-1121 | a13 | LOYAL | 0.7 | e1 |
| Kookmin | 010-1221-1732 | a21 | LOYAL | 1 | e2 |

**EMPLOYEE**

| empl_id | empl_name |
| --- | --- |
| e1 | Sony |
| e2 | Messi |

**ACCOUNT_CARD**

| account_id | card_id |
| --- | --- |
| a11 | c101 |
| a11 | c102 |
| a13 | c103 |
| a21 | c201 |
| a21 | c202 |

나누어진 각각의 테이블을 확인해보면 각각이 3NF를 만족한다.

## BCNF

**모든 유효한 Non-trivial Functional Dependency X → Y 는 X가 Super Key여야 한다.**

3NF를 만족하는 테이블 (나머지 2개의 테이블은 BCNF도 만족하므로 생략)

**EMPLOYEE_ACCOUNT**

| bank_name | account_num | account_id | class | ratio | empl_id |
| --- | --- | --- | --- | --- | --- |
| Woori | 010-9231-1121 | a11 | BRONZE | 0.1 | e1 |
| Woori | 102-992-180125 | a11 | SILVER | 0.2 | e1 |
| Kookmin | 010-9231-1121 | a13 | LOYAL | 0.7 | e1 |
| Kookmin | 010-1221-1732 | a21 | LOYAL | 1 | e2 |

아직도 중복이 존재한다.

- 계좌마다 등급(class)가 존재한다.
    - 국민 : STAR → PRESTIGE → LOYAL
    - 우리 : BRONZE → SILVER → GOLD

**테이블에서 {class} → {bank_name}이지만, {class}는 EMPLOYEE_ACCOUNT의 super key가 아니다.**

**따라서 BCNF를 위반한다.**

이를 해결하기 위해서는 역시 테이블을 분리해야 한다.

**EMPLOYEE_ACCOUNT**

| account_num | account_id | class | ratio | empl_id |
| --- | --- | --- | --- | --- |
| 010-9231-1121 | a11 | BRONZE | 0.1 | e1 |
| 102-992-180125 | a11 | SILVER | 0.2 | e1 |
| 010-9231-1121 | a13 | LOYAL | 0.7 | e1 |
| 010-1221-1732 | a21 | LOYAL | 1 | e2 |

**ACCOUNT_CLASS**

| class | bank_name |
| --- | --- |
| BRONZE | Woori |
| SILVER | Woori |
| GOLD | Woori |
| STAR | Kookmin |
| PRESTIGE | Kookmin |
| LOYAL | Kookmin |

**EMPLOYEE**

| empl_id | empl_name |
| --- | --- |
| e1 | Sony |
| e2 | Messi |

**ACCOUNT_CARD**

| account_id | card_id |
| --- | --- |
| a11 | c101 |
| a11 | c102 |
| a13 | c103 |
| a21 | c201 |
| a21 | c202 |

최종적으로 4개 테이블은 모두 BCNF를 만족한다.

## Denormalization

정규화 과정에서 테이블을 너무 많이 분리하면 여러 테이블을 동시에 JOIN해야 해서 성능 저하가 발생할 수 있다.

관리하기도 까다로울 수 있다.

전략적으로 정규화를 어느 정도는 포기할 수 있다.

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 가장 짧은 부분합**

[https://www.codetree.ai/missions/8/problems/shortest-subtotal/introduction](https://www.codetree.ai/missions/8/problems/shortest-subtotal/introduction)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/shortest-subtotal/introduction)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;   //  원소 개수
    private static int s;   //  원소 합 s

    private static int[] nums;  //  원소

    private static int start = 0;
    private static int end = 0;
    private static int sum = 0;

    private static int len = Integer.MAX_VALUE; //  원소들 합이 s 이상이 되는 가장 짧은 구간 길이
    
    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        s = Integer.parseInt(tokens.nextToken());

        nums = new int[n];
        tokens = new StringTokenizer(br.readLine());

        for(int i = 0; i < n; i++)
            nums[i] = Integer.parseInt(tokens.nextToken());

        sum = nums[end];

        while(start <= end && end < n) {
            if(sum >= s) {   // 구간 합이 s 이상일 경우
                // System.out.println("start = " + start + ", end = " + end);
                // System.out.println("sum = " + sum);

                len = Math.min(len, end - start + 1);
                sum -= nums[start];
                start++;
            }
            else {  //  구간 합이 s 미만일 경우
                end++;
                if(end < n)
                    sum += nums[end];
            }
        }

        System.out.println(len == Integer.MAX_VALUE ? -1 : len);
    }   //  main-end
}   //  Main-class-end
```

**CO{)E TREE 연속하는 정수 n개의 합**

[https://www.codetree.ai/missions/8/problems/sum-of-consecutive-n-integers/description](https://www.codetree.ai/missions/8/problems/sum-of-consecutive-n-integers/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/sum-of-consecutive-n-integers/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;   //  원소 개수
    private static int m;   //  원소 합 s

    private static int[] nums;  //  원소

    private static int start = 0;
    private static int end = 0;
    private static int sum = 0;

    private static int ans = 0; //  원소들 합이 m이 되는 경우의 수
    
    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        m = Integer.parseInt(tokens.nextToken());

        nums = new int[n];
        tokens = new StringTokenizer(br.readLine());

        for(int i = 0; i < n; i++)
            nums[i] = Integer.parseInt(tokens.nextToken());

        sum = nums[end];

        while(start <= end && end < n) {
            if(sum == m) {
                ans++;
                sum -= nums[start];
                start++;
                end++;
                if(end < n)
                    sum += nums[end];
            }
            else if(sum > m) {
                sum -= nums[start];
                start++;
            }
            else {
                end++;
                if(end < n)
                    sum += nums[end];
            }
        }

        System.out.println(ans);
    }   //  main-end
}   //  Main-class-end
```

**CO{)E TREE 정수 두 개의 합 2**

[https://www.codetree.ai/missions/8/problems/sum-of-two-integers-2/description](https://www.codetree.ai/missions/8/problems/sum-of-two-integers-2/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/sum-of-two-integers-2/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;   //  원소 개수
    private static int k;   //  원소 합 k

    private static int[] nums;  //  원소

    private static int ans = 0; //  원소들 합이 m이 되는 경우의 수
    
    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        k = Integer.parseInt(tokens.nextToken());

        nums = new int[n];

        for(int i = 0; i < n; i++)
            nums[i] = Integer.parseInt(br.readLine());

        Arrays.sort(nums);

        for(int i = 0; i < n - 1; i++) {
            int num = nums[i];

            if(k - num < num)
                break;

            int from = i + 1;
            int to = n - 1;
            int target = k - num;
            
            ans += binarySearch(from, to, target);  //  [from, to] 범위에서 target 이하 정수 개수 찾기
        }

        System.out.println(ans);
    }   //  main-end

    private static int binarySearch(int from, int to, int target) {
        int idx = -1;

        int start = from;
        int end = to;

        while(start <= end) {
            int mid = (start + end) / 2;

            if(nums[mid] <= target) {
                idx = mid;
                start = mid + 1;
            }
            else end = mid - 1;
        }

        return idx == -1 ? 0 : idx - from + 1;
    }
}   //  Main-class-end
```