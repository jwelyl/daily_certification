# 25_03_27_daily_certification

# Problem Solving (Algorithm & SQL)

```sql
CREATE TABLE colors (
    color VARCHAR(20),
    name VARCHAR(20),
    `order` INT
);

INSERT INTO colors (color, name, `order`) VALUES
('green', 'sg1', 1),
('green', 'sg2', 2),
('green', 'sg3', 3),
('green', 'sg4', 4),
('orange', 'so1', 12),
('orange', 'so2', 14),
('orange', 'so3', 18),
('orange', 'so4', 19),
('orange', 'so5', 32),
('red', 'sr1', 6),
('red', 'sr2', 7),
('red', 'sr3', 9);

select * from colors;

SELECT
  color,
  GROUP_CONCAT(name ORDER BY `order` SEPARATOR '-') AS res
FROM colors
GROUP BY color;

```