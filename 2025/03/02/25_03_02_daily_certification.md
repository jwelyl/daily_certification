# 25_03_02_daily_certification

# Problem Solving (Algorithm & SQL)

### 프로그래머스 연속 펄스 부분 수열의 합

[](https://school.programmers.co.kr/learn/courses/30/lessons/161988)

### DNC

```java
class Solution {
    private int[] arr1;
    private int[] arr2;
    
    public long solution(int[] sequence) {
        long answer = 0;
        
        arr1 = new int[sequence.length + 1];
        arr2 = new int[sequence.length + 1];
        
        for(int i = 1; i <= sequence.length; i++) {
            arr1[i] = (i % 2 == 0 ? -sequence[i - 1] : sequence[i - 1]);
            arr2[i] = -arr1[i];
        }
        
        answer = Math.max(dnc(arr1, 1, sequence.length), dnc(arr2, 1, sequence.length));
        
        return answer;
    }

    private long dnc(int[] arr, int start, int end) {
        if(start == end)
            return arr[start];
        
        int mid = (start + end) / 2;
        
        long leftMax = dnc(arr, start, mid);
        long rightMax = dnc(arr, mid + 1, end);
        
        long leftPS = arr[mid];
        long rightPS = arr[mid + 1];
        long maxLeftPS = leftPS;
        long maxRightPS = rightPS;
        
        for(int i = mid - 1; i >= start; i--) {
            leftPS += arr[i];
            
            maxLeftPS = Math.max(maxLeftPS, leftPS);
        }
        
        for(int i = mid + 2; i <= end; i++) {
            rightPS += arr[i];
            
            maxRightPS = Math.max(maxRightPS, rightPS);
        }
        
        return Math.max(Math.max(leftMax, rightMax), maxLeftPS + maxRightPS);
    }
}
```

### 프로그래머스 그룹별 조건에 맞는 식당 목록 출력하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/131124)

**RANK**

```sql
select mp.member_name, rr.review_text, date_format(rr.review_date, '%Y-%m-%d') as review_date
from member_profile mp
inner join
rest_review rr
on mp.member_id = rr.member_id
where mp.member_id in (
    select member_id
    from (
        select member_id, rank() over(order by count(*) desc) as ranks
        from rest_review
        group by member_id
    ) sub
    where ranks = 1
)
order by review_date, rr.review_text;
```

**동점자 무시**

```sql
select mp.member_name, rr.review_text, date_format(rr.review_date, '%Y-%m-%d') as review_date
from member_profile mp inner join rest_review rr
on mp.member_id = rr.member_id
where mp.member_id = (
    select member_id
    from rest_review
    group by member_id
    order by count(*) desc
    limit 1
)
order by review_date, rr.review_text;
```

### 프로그래머스 저자 별 카테고리 별 매출액 집계하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/144856)

```sql
# select a.author_id, a.author_name, b.category
# from ((book b inner join book_sales bs on b.book_id = bs.book_id) 
# inner join author a
# on b.author_id = a.author_id)
# group by a.author_name, b.category;

# select b.book_id, a.author_id, a.author_name, b.category, bs.sales, b.price
# from book b inner join book_sales bs on b.book_id = bs.book_id inner join author a on b.author_id = a.author_id
# order by book_id desc;

# select price from book where book_id = 2;

# select sum(sales) from book_sales where book_id = 2;

    # select b.book_id, a.author_id, a.author_name, b.category, sum(bs.sales) * b.price as book_total_sales
    # from book b inner join book_sales bs on b.book_id = bs.book_id inner join author a on b.author_id = a.author_id
    # group by b.book_id;

select res.author_id, res.author_name, res.category, sum(book_total_sales) as total_sales
from (
    select a.author_id, a.author_name, b.category, sum(bs.sales) * b.price as book_total_sales
    from book b inner join book_sales bs on b.book_id = bs.book_id inner join author a on b.author_id = a.author_id
    where date_format(bs.sales_date, '%Y-%m') = '2022-01' 
    group by b.book_id
) res
group by res.author_id, res.category
order by res.author_id, res.category desc;

-- 코드를 입력하세요
# select a.author_id, a.author_name, b.category, sum(b.price * bs.sales) as total_sales
# from (book b inner join book_sales bs on b.book_id = bs.book_id) 
# inner join author a
# on b.author_id = a.author_id
# group by a.author_name, b.category
# order by a.author_id, b.category desc;

# select * from
# author;

# select * from
# book_sales;
```