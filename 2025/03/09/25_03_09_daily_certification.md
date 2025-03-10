# 25_03_09_daily_certification

# Problem Solving (Algorithm & SQL)

### 프로그래머스 조회수가 가장 많은 중고거래 게시판의 첨부파일 조회하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/164671)

```sql
-- 코드를 입력하세요
select concat('/home/grep/src/', t2.board_id, '/', t2.file_id, t2.file_name, t2.file_ext) as file_path
from used_goods_board t1 inner join used_goods_file t2
on t1.board_id = t2.board_id
where t2.board_id = (
    select board_id
    from used_goods_board
    order by views desc
    limit 1
)
order by t2.file_id desc;
```