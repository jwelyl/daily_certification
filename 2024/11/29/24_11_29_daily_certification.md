# 24_11_29_daily_certification

# Database

# SQL

| **EMPLOYEE** |  |  |  |  |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| id | name | birth_date | sex | position | salary | dept_id |

| **PROJECT** |  |  |  |  |
| --- | --- | --- | --- | --- |
| id | name | leader_id | start_date | end_date |

| **WORKS_ON** |  |
| --- | --- |
| proj_id | empl_id |

| **DEPARTMENT** |  |  |
| --- | --- | --- |
| id | name | leader_id |

## SELECT

프로젝트 2002를 리딩(Leading)하고 있는 임직원의 ID,이름, 직군은?

```sql
SELECT e.id, e.name, e.position
FROM EMPLOYEE e, PROJECT p
WHERE p.id = 2002 and p.leader_id = e.id;
```

## SUBQUERY

ID가 14인 임직원보다 생일이 빠른 임직원의 ID, 이름, 생일은?

```sql
SELECT e1.id, e1.name, e1.birth_date
FROM EMPLOYEE e1
WHERE e1.birth_date < (
	SELECT e2.birth_date 
	FROM EMPLOYEE e2 
	WHERE e2.id = 14
);
```

ID가 1인 임직원과 같은 부서, 같은 성별인 임직원들의 ID와 이름과 직군을 알고 싶다.

```sql
SELECT e1.id, e1.position
FROM EMPLOYEE e1
WHERE (e1.dept_id, e1.sex) in (
	SELECT e2.dept_id, e2.sex 
	FROM EMPLOYEE e2 
	WHERE e2.id = 1
);
```

ID가 5인 임직원과 같은 프로젝트에 참여한 임직원들의 ID를 알고 싶다.

???

```sql
SELECT DISTINCT e.id
FROM EMPLOYEE e, WORKS_ON w1
WHERE e.id = w1.empl_id AND w1.proj_id = (
	SELECT w2.proj_id
	FROM WORKS_ON w2
	WHERE w2.empl_id = 5
);
```

OK

```sql
SELECT DISTINCT w1.empl_id
FROM WORKS_ON w1
WHERE w1.empl_id != 5 AND w1.proj_id IN (
	SELECT w2.proj_id
	FROM WORKS_ON w2
	WHERE w2.empl_id = 5
);
```

ID가 5인 임직원과 같은 프로젝트에 참여한 임직원들의 ID와 이름을 알고 싶다.

???

```sql
SELECT DISTINCT e.id, e.name
FROM EMPLOYEE e, WORKS_ON w1
WHERE e.id != 5 AND e.id = w1.empl_id AND w1.proj_id IN (
	SELECT w2.proj_id
	FROM WORKS_ON w2
	WHERE w2.empl_id = 5
);
```

OK

```sql
SELECT e.id, e.name
FROM EMPLOYEE e
WHERE e.id IN (
	SELECT DISTINCT w1.empl_id
	FROM WORKS_ON w1
	WHERE w1.empl_id != 5 AND w1.proj_id IN (
		SELECT w2.proj_id
		FROM WORKS_ON w2
		WHERE w2.empl_id = 5
	)
);
```

```sql
SELECT e.id, e.name
FROM EMPLOYEE e, (
    SELECT DISTINCT w1.empl_id
    FROM WORKS_ON w1
    WHERE w1.empl_id != 5 AND w1.proj_id IN (
        SELECT w2.proj_id
        FROM WORKS_ON w2
        WHERE w2.empl_id = 5
    )
) AS DISTINCT_E
WHERE e.id = DISTINCT_E.empl_id;
```

ID가 7 또는 12인 임직원이 참여한 프로젝트의 ID와 이름?

```sql
SELECT p.id, p.name
FROM PROJECT p
WHERE p.id in (
	SELECT w.proj_id
	FROM WORKS_ON w
	WHERE w.empl_id = 7 OR w.empl_id = 12
);
```

```sql
SELECT p.id, p.name
FROM PROJECT p
WHERE EXISTS (
	SELECT *
	FROM WORKS_ON w
	WHERE w.proj_i = p.id AND w.empl_id IN (7, 12)
);
```

2000년대생이 없는 부서의 ID와 이름?

```sql
SELECT d.id, d.name
FROM DEPARTMENT d
WHERE NOT EXIST (
	SELECT *
	FROM EMPLOYEE e
	WHERE e.dept_id = d.id AND e.birth_date >= '2000-01-01'
);
```

```sql
SELECT d.id, d.name
FROM DEPARTMENT d
WHERE d.id NOT IN (
	SELECT e.dept_id
	FROM EMPLOYEE e
	WHERE e.birth_date >= '2000-01-01'
);
```

리더보다 높은 연봉을 받는 부서원을 가진 리더의 ID와 이름과 연봉을 알고 싶다.

```sql
SELECT e.id, e.name, e.salary
FROM DEPARTMENT d, EMPLOYEE e
WHERE d.leader_id = e.id AND e.salary < ANY (
	SELECT salary
	FROM EMPLOYEE
	WHRE id <> d.leader_id AND dept_id = e.dept_id
);
```

리더보다 높은 연봉을 받는 부서원을 가진 리더의 ID와 이름과 연봉과 해당 부서의 최고 연봉을 알고 싶다.

```sql
SELECT e.id, e.name, e.salary, (
	SELECT max(salary)
	FROM EMPLOYEE
	WHERE dept_id = e.dept_id
) AS dept_max_salary
FROM DEPARTMENT d, EMPLOYEE e
WHERE d.leader_id = e.id AND e.salary < ANY (
	SELECT salary
	FROM EMPLOYEE
	WHRE id <> d.leader_id AND dept_id = e.dept_id
);
```

ID가 13인 임직원과 한번도 같은 프로젝트에 참여하지 못한 임직원들의 ID, 이름, 직군을 알고 싶다.

???

```sql
SELECT DISTINCT e.id, e.name, e.position
FROM EMPLOYEE e, WORKS_ON w1
WHERE e.id = w1.empl_id AND w1.proj_id NOT IN (
	SELECT w.proj_id
	FROM WORKS_ON w
	WHERE w.empl_id = 13
);
```

OK

```sql
SELECT DISTINCT e.id, e.name, e.position
FROM EMPLOYEE e, WORKS_ON w1
WHERE e.id = w1.empl_id AND w1.proj_id <> ALL (
	SELECT w.proj_id
	FROM WORKS_ON w
	WHERE w.empl_id = 13
);
```

## NULL, Three-valued Logic

| **AND** |  |  |  |
| --- | --- | --- | --- |
|  | TRUE | FALSE | UNKNOWN |
| TRUE | TRUE | FALSE | UNKNOWN |
| FALSE | FALSE | FALSE | FALSE |
| UNKNOWN | UNKNOWN | FALSE | UNKNOWN |

| **OR** |  |  |  |
| --- | --- | --- | --- |
|  | TRUE | FALSE | UNKNOWN |
| TRUE | TRUE | TRUE | TRUE |
| FALSE | TRUE | FALSE | UNKNOWN |
| UNKNOWN | TRUE | UNKNOWN | UNKNOWN |

| **NOT** |  |
| --- | --- |
| TRUE | FALSE |
| FALSE | TRUE |
| UNKNOWN | UNKNOWN |

## JOIN

### Implicit JOIN vs Explicit JOIN

ID가 1인 임직원이 속한 부서의 이름은?

**Implicit JOIN**

```sql
SELECT d.name
FROM EMPLOYEE e, DEPARTMENT d
WHERE e.id = 1 AND e.dept_id = d.id;
```

**Explicit JOIN**

```sql
SELECT d.name
FROM EMPLOYEE e
JOIN DEPARTMENT d ON e.dept_id = d.id
WHERE e.id = 1;
```

### INNER JOIN vs OUTER JOIN

**INNER JOIN**

기본적인 JOIN, INNER를 생략하면 INNER JOIN이 된다.

| **EMPLOYEE** |  |  |  |  |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| id | name | birth_date | sex | position | salary | dept_id |
| 1 | MESSI |  |  |  |  | 1003 |
| 2 | JANE |  |  |  |  | 1004 |
| 3 | JENNY |  |  |  |  | 1003 |
| 4 | BROWN |  |  |  |  | 1001 |
| 13 | JISUNG |  |  |  |  | 1005 |
| 15 | SIMON |  |  |  |  | null |

| **DEPARTMENT** |  |  |
| --- | --- | --- |
| id | name | leader_id |
| 1001 | HQ | 4 |
| 1002 | HR | null |
| 1003 | DEV | 1 |
| 1004 | DESIGN | 3 |
| 1005 | PRODUCT | 13 |

```sql
SELECT *
FROM EMPLOYEE e
INNER JOIN DEPARTMENT d
ON e.dept_id = d.id;
```

| id | name | birth_date | sex | position | salary | dept_id | id | name | leader_id |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | MESSI |  |  |  |  | 1003 | 1003 | DEV | 1 |
| 2 | JANE |  |  |  |  | 1004 | 1004 | DESIGN | 3 |
| 3 | JENNY |  |  |  |  | 1003 | 1003 | DEV | 1 |
| 4 | BROWN |  |  |  |  | 1001 | 1001 | HQ | 4 |
| 13 | JISUNG |  |  |  |  | 1005 | 1005 | PRODUCT | 13 |

**LEFT OUTER JOIN**

```sql
SELECT *
FROM EMPLOYEE e
LEFT OUTER JOIN DEPARTMENT d
ON e.dept_id = d.id;
```

| id | name | birth_date | sex | position | salary | dept_id | id | name | leader_id |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | MESSI |  |  |  |  | 1003 | 1003 | DEV | 1 |
| 2 | JANE |  |  |  |  | 1004 | 1004 | DESIGN | 3 |
| 3 | JENNY |  |  |  |  | 1003 | 1003 | DEV | 1 |
| 4 | BROWN |  |  |  |  | 1001 | 1001 | HQ | 4 |
| 13 | JISUNG |  |  |  |  | 1005 | 1005 | PRODUCT | 13 |
| 15 | SIMON |  |  |  |  | null | null | null | null |

**RIGHT OUTER JOIN**

```sql
SELECT *
FROM EMPLOYEE e
RIGHT OUTER JOIN DEPARTMENT d
ON e.dept_id = d.id;
```

| id | name | birth_date | sex | position | salary | dept_id | id | name | leader_id |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | MESSI |  |  |  |  | 1003 | 1003 | DEV | 1 |
| 2 | JANE |  |  |  |  | 1004 | 1004 | DESIGN | 3 |
| 3 | JENNY |  |  |  |  | 1003 | 1003 | DEV | 1 |
| 4 | BROWN |  |  |  |  | 1001 | 1001 | HQ | 4 |
| 13 | JISUNG |  |  |  |  | 1005 | 1005 | PRODUCT | 13 |
| null | null | null | null | null | null | null | 1002 | HR | null |

**FULL OUTER JOIN (MySQL은 미지원)**

```sql
SELECT *
FROM EMPLOYEE e
FULL OUTER JOIN DEPARTMENT d
ON e.dept_id = d.id;
```

| id | name | birth_date | sex | position | salary | dept_id | id | name | leader_id |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | MESSI |  |  |  |  | 1003 | 1003 | DEV | 1 |
| 2 | JANE |  |  |  |  | 1004 | 1004 | DESIGN | 3 |
| 3 | JENNY |  |  |  |  | 1003 | 1003 | DEV | 1 |
| 4 | BROWN |  |  |  |  | 1001 | 1001 | HQ | 4 |
| 13 | JISUNG |  |  |  |  | 1005 | 1005 | PRODUCT | 13 |
| 15 | SIMON |  |  |  |  | null | null | null | null |
| null | null | null | null | null | null | null | 1002 | HR | null |

### EQUI JOIN, USING

| **EMPLOYEE** |  |  |  |  |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| id | name | birth_date | sex | position | salary | dept_id |
| 1 | MESSI |  |  |  |  | 1003 |
| 2 | JANE |  |  |  |  | 1004 |
| 3 | JENNY |  |  |  |  | 1003 |
| 4 | BROWN |  |  |  |  | 1001 |
| 13 | JISUNG |  |  |  |  | 1005 |
| 15 | SIMON |  |  |  |  | null |

| **DEPARTMENT** |  |  |
| --- | --- | --- |
| dept_id | name | leader_id |
| 1001 | HQ | 4 |
| 1002 | HR | null |
| 1003 | DEV | 1 |
| 1004 | DESIGN | 3 |
| 1005 | PRODUCT | 13 |

```sql
SELECT *
FROM EMPLOYEE e
INNER JOIN DEPARTMENT d
USING(dept_id);;
```

| dept_id | id | name | birth_date | sex | position | salary | name | leader_id |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1003 | 1 | MESSI |  |  |  |  | DEV | 1 |
| 1004 | 2 | JANE |  |  |  |  | DESIGN | 3 |
| 1003 | 3 | JENNY |  |  |  |  | DEV | 1 |
| 1001 | 4 | BROWN |  |  |  |  | HQ | 4 |
| 1005 | 13 | JISUNG |  |  |  |  | PRODUCT | 13 |

### NATURAL JOIN

두 테이블에서 같은 이름을 가지는 모든 attribute pair에 대해서 equi join 실행

JOIN 조건 따로 명시 X

**NATURAL INNER JOIN**

**NATURAL LEFT OUTER JOIN**

**NATURAL RIGHT OUTER JOIN**

**NATURAL FULL OUTER JOIN**

**NATURAL INNER JOIN**

| **EMPLOYEE** |  |  |  |  |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| id | name | birth_date | sex | position | salary | dept_id |
| 1 | MESSI |  |  |  |  | 1003 |
| 2 | JANE |  |  |  |  | 1004 |
| 3 | JENNY |  |  |  |  | 1003 |
| 4 | BROWN |  |  |  |  | 1001 |
| 13 | JISUNG |  |  |  |  | 1005 |
| 15 | SIMON |  |  |  |  | null |

| **DEPARTMENT** |  |  |
| --- | --- | --- |
| dept_id | dept_name | leader_id |
| 1001 | HQ | 4 |
| 1002 | HR | null |
| 1003 | DEV | 1 |
| 1004 | DESIGN | 3 |
| 1005 | PRODUCT | 13 |

```sql
SELECT *
FROM EMPLOYEE e
INNER JOIN DEPARTMENT d
USING(dept_id);;
```

| dept_id | id | name | birth_date | sex | position | salary | name | leader_id |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1003 | 1 | MESSI |  |  |  |  | DEV | 1 |
| 1004 | 2 | JANE |  |  |  |  | DESIGN | 3 |
| 1003 | 3 | JENNY |  |  |  |  | DEV | 1 |
| 1001 | 4 | BROWN |  |  |  |  | HQ | 4 |
| 1005 | 13 | JISUNG |  |  |  |  | PRODUCT | 13 |

| **EMPLOYEE** |  |  |  |  |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| id | name | birth_date | sex | position | salary | dept_id |

| **PROJECT** |  |  |  |  |
| --- | --- | --- | --- | --- |
| id | name | leader_id | start_date | end_date |

| **WORKS_ON** |  |
| --- | --- |
| proj_id | empl_id |

| **DEPARTMENT** |  |  |
| --- | --- | --- |
| id | name | leader_id |

ID가 1003인 부서에 속하는 임직원 중 리더를 제외한 부서원의 ID, 이름, 연봉을 알고 싶다.

```sql
SELECT e.id, e.name, e.salary
FROM EMPLOYEE e
INNER JOIN DEPARTMENT d
ON e.dept_id = d.id
WHERE d.id = 1003 AND e.id != d.leader_id;
```

ID가 2001인 프로젝트에 참여한 임직원들의 이름과 직군과 소속 부서 이름을 알고 싶다.

```sql
SELECT e.name, e.position, d.name
FROM WORKS_ON w JOIN EMPLOYEE e ON w.empl_id = e.id
LEFT JOIN DEPARTMENT d ON e.dept_id = d.id
WHERE w.proj_id = 2001;
```

## Aggregate Function

프로젝트 2002에 참여한 임직원 수와 최대 연봉과 최소 연봉과 평균 연봉을 알고 싶다.

```sql
SELECT COUNT(*), MAX(salary), MIN(salary), AVG(salary)
FROM WORKS_ON w INNER JOIN EMPLOYEE e ON w.empl_id = e.id
WHERE w.proj_id = 2002;
```

### GROUP BY

각 프로젝트 별로 참여한 임직원 수와 최대 연봉과 최소 연봉과 평균 연봉을 알고 싶다.

```sql
SELECT **w.proj_id**, COUNT(*), MAX(salary), MIN(salary), AVG(salary)
FROM WORKS_ON w INNER JOIN EMPLOYEE e ON w.empl_id = e.id
GROUP BY **w.proj_id**;
```

GROUP BY의 기준 attribute는 SELECT attribute에 포함되어야 한다.

### HAVING

프로젝트 참여 인원이 7명 이상인 프로젝트들에 대해서 각 프로젝트에 참여한 임직원 수와 최대 연봉과 최소 연봉과 평균 연봉을 알고 싶다.

```sql
SELECT **w.proj_id**, COUNT(*), MAX(salary), MIN(salary), AVG(salary)
FROM WORKS_ON w INNER JOIN EMPLOYEE e ON w.empl_id = e.id
GROUP BY **w.proj_id**
HAVING COUNT(*) >= 7;
```

각 부서별 인원수를 인원수가 많은 순서대로 정렬해서 알고 싶다.

```sql
SELECT e.dept_id, COUNT(*) AS empl_cnt
FROM EMPLOYEE e
GROUP BY e.dept_id
ORDER BY empl_cnt DESC;
```

각 부서별-성별 인원수를 인원수가 많은 순서대로 정렬해서 알고 싶다.

```sql
SELECT e.dept_id, e.sex, COUNT(*) AS empl_cnt
FROM EMPLOYEE e
GROUP BY e.dept_id, e.sex
ORDER BY empl_cnt DESC;
```

회사 전체 평균 연봉보다 평균 연봉이 적은 부서들의 평균 연봉을 알고 싶다.

```sql
# 회사 전체 평균 연봉
SELECT AVG(salary) FROM EMPLOYEE;

# 각 부서별 평균 연봉
SELECT e.dept_id, AVG(e.salary)
FROM EMPLOYEE e
GROUP BY e.dept_id;

# 정답 쿼리
SELECT e.dept_id, AVG(e.salary)
FROM EMPLOYEE e
GROUP BY e.dept_id
HAVING AVG(e.salary) < (
	SELECT AVG(salary) 
	FROM EMPLOYEE;
);
```

각 프로젝트별로 프로젝트에 참여한 90년대생들의 수와 이들의 평균 연봉을 알고 싶다.

```sql
SELECT w.proj_id, COUNT(*), ROUND(AVG(e.salary), 0)
FROM WORKS_ON w INNER JOIN EMPLOYEE e ON w.empl_id = e.id
WHERE e.birth_date BETWEEN '1990-01-01' AND '1999-12-31'
GROUP BY w.proj_id
ORDER BY w.proj_id;
```

프로젝트 참여 인원이 7명 이상인 프로젝트에 한정해서 각 프로젝트별로 프로젝트에 참여한 90년대생들의 수와 이들의 평균 연봉을 알고 싶다.

```sql
# 프로젝트 참여 인원이 7명 이상인 프로젝트 모음
SELECT proj_id
FROM WORKS_ON
GROUP BY proj_id
HAVING COUNT(*) >= 7;

# 정답 쿼리
SELECT w.proj_id, COUNT(*), ROUND(AVG(e.salary), 0)
FROM WORKS_ON w INNER JOIN EMPLOYEE e ON w.empl_id = e.id
WHERE e.birth_date BETWEEN '1990-01-01' AND '1999-12-31'
AND w.proj_id IN (
	SELECT proj_id
	FROM WORKS_ON
	GROUP BY proj_id
	HAVING COUNT(*) >= 7
)
GROUP BY w.proj_id
ORDER BY w.proj_id;
```