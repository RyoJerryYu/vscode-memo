
# Postgres 中的窗口函数
## SQL 处理顺序：
- FROM
- WHERE
- Group By
- Having
- Select
    - 表达式
    - Distinct
- order by
    - offset - fetch

## 窗口函数语法
```
<窗口函数>
OVER (
    PARTITION BY
    ORDER BY
)
```

窗口函数中可用：
聚合函数：① 聚合函数（SUM、AVG、COUNT、MAX、MIN）
内置函数：② RANK、DENSE_RANK、ROW_NUMBER 等专用窗口函

## 窗口函数中使用聚合函数时，不使用 order by 相当于 group 中每一行填聚合后的值

```sql
select s.*, v.avgscore
from score s
left join (
    select subject, avg(grade) as avgscore
    from score
    group by subject
) v on s.subject = v.subject
```

相当于

```sql
select s.*, avg(grade) over(partition by subject) as avgscore
from score s
```

## 窗口函数中聚合函数使用 order by 后，会在窗口内应用聚合函数

略

```sql
with (
    select fc.id as comment_id , fc.feed_id, count(fcr.id) as cnt 
    from feed_comment fc join feed_comment_reaction fcr on fc.id = fcr.comment_id 
    where fc.feed_id = any('{1,2,3}')
    group by fc
) as fcid
select fcid.comment_id, RANK() over (partition by fcid.feed_id order by fcid.cnt desc) as rnk
from fcid
where rnk <= 3
```

```sql

    select fc.id as comment_id , fc.feed_id as feed_id, count(fcr.id) as cnt 
    from feed_comment fc join feed_comment_reaction fcr on fc.id = fcr.comment_id 
    where fc.feed_id = any('{1,2,3}')
    group by fc.id, fc.feed_id
```

```sql

select * from feed_comment fc where fc.id in (
    with (
        select fc.id as comment_id , fc.feed_id as feed_id, count(fcr.id) as cnt 
        from feed_comment fc join feed_comment_reaction fcr on fc.id = fcr.comment_id 
        where fc.feed_id = any('{1,2,3}')
        group by fc.id
    ) as fcid
    select comment_id from (
        select fcid.comment_id, RANK() over (partition by fcid.feed_id order by fcid.cnt desc) as rnk
        from fcid
    ) as fcid where rnk <= 3
);

```
