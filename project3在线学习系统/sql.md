```sql
-- 查询视频浏览记录
select id, use_id, course_id, browse_time, video_id from browsing_history bh 
join (
select course_id cid, max(browse_time) max_time from browsing_history 
where use_id = 1
group by course_id
) t on(t.cid=bh.course_id)
where use_id = 1 and browse_time=max_time
order by browse_time desc

--查询用户在此课程上学了多长时间
select TIMESTAMPDIFF(MINUTE,min(browse_time),max(browse_time))
from browsing_history 
where use_id=1 and course_id=1


--查询用户已经学习了课程的百分比
select 
from (select) t, () t1
where use_id=1 and course_id=1


-- 根据视频id  查询视频对应的课程 
select co.* from video v 
join chapter ch on(v.chapter_id=ch.id) 
join course co on(co.id=ch.course_id)
where v.id=1;

-- 插入用户2
insert into user
values(2,'guanyu','588d686105cf488835925c879935c3ff', '关羽', '学生','男','北京市 - 北京市 - 崇文区','好好学习，天天向上', null, '2017-07-04 00:00:00','5138915196502228097');
-- 插入用户3
insert into user
values(3,'liubei','588d686105cf488835925c879935c3ff', '刘备', '学生','男','北京市 - 北京市 - 崇文区','好好学习，天天向上', null, '2017-07-04 00:00:00','5138915196502228097');
-- 插入用户25
insert into user
values(25,'caocao','588d686105cf488835925c879935c3ff', '曹操', '学生','男','北京市 - 北京市 - 崇文区','好好学习，天天向上', null, '2017-07-04 00:00:00','5138915196502228097');
```

### 课程收藏的数据

```sql
insert into collect_course values(null, 1, 1, date_sub(now(), interval floor(rand()*500) day));
insert into collect_course values(null, 1, 2, date_sub(now(), interval floor(rand()*500) day));
insert into collect_course values(null, 1, 3, date_sub(now(), interval floor(rand()*500) day));
insert into collect_course values(null, 1, 4, date_sub(now(), interval floor(rand()*500) day));
insert into collect_course values(null, 1, 5, date_sub(now(), interval floor(rand()*500) day));

```

### 试题表数据

```sql
 insert into question values(null, 1,  date_sub(now(), interval  floor(rand()*500) day) , concat('数据库试题表为什么没有数据！！!',rand()), 5, 'A', concat('因为数据库里面数据少',rand()), 1, 1, floor(rand()*25), 1);
  insert into question values(null, 1,  date_sub(now(), interval  floor(rand()*500) day) , concat('数据库试题表为什么没有数据！！!',rand()), 5, 'B', concat('因为数据库里面数据少',rand()), 1, 1, floor(rand()*25), 1);
   insert into question values(null, 1,  date_sub(now(), interval  floor(rand()*500) day) , concat('数据库试题表为什么没有数据！！!',rand()), 5, 'C', concat('因为数据库里面数据少',rand()), 1, 1, floor(rand()*25), 1);
    insert into question values(null, 1,  date_sub(now(), interval  floor(rand()*500) day) , concat('数据库试题表为什么没有数据！！!',rand()), 5, 'D', concat('因为数据库里面数据少',rand()), 1, 1, floor(rand()*25), 1);
 
 
 
insert into paper_question values(3, 1, 10);
insert into paper_question values(3, 2, 10);
insert into paper_question values(3, 3, 10);
insert into paper_question values(3, 4, 10);
insert into paper_question values(3, 5, 10);
insert into paper_question values(3, 6, 10);
insert into paper_question values(3, 7, 10);
insert into paper_question values(3, 8, 10);
insert into paper_question values(3, 9, 10);
insert into paper_question values(3, 10, 10);
insert into paper_question values(3, 11, 10);
insert into paper_question values(3, 12, 10);
insert into paper_question values(3, 13, 10);

insert into question_options values(null, floor(rand()*1038), concat('讨厌造数据1', rand()));
insert into question_options values(null, floor(rand()*1038), concat('讨厌造数据1', rand()));
insert into question_options values(null, floor(rand()*1038), concat('讨厌造数据1', rand()));
insert into question_options values(null, floor(rand()*1038), concat('讨厌造数据1', rand()));
insert into question_options values(null, floor(rand()*1038), concat('讨厌造数据1', rand()));
insert into question_options values(null, floor(rand()*1038), concat('讨厌造数据1', rand()));
insert into question_options values(null, floor(rand()*1038), concat('讨厌造数据1', rand()));
insert into question_options values(null, floor(rand()*1038), concat('讨厌造数据1', rand()));
insert into question_options values(null, floor(rand()*1038), concat('讨厌造数据1', rand()));
insert into question_options values(null, floor(rand()*1038), concat('讨厌造数据1', rand()));
insert into question_options values(null, floor(rand()*1038), concat('讨厌造数据1', rand()));
insert into question_options values(null, floor(rand()*1038), concat('讨厌造数据1', rand()));
insert into question_options values(null, floor(rand()*1038), concat('讨厌造数据1', rand()));
insert into question_options values(null, floor(rand()*1038), concat('讨厌造数据1', rand()));

update q1 set q1.answer=q2.ans from 
with temp as (
select q.id , t.ans from question q join(
CREATE TEMPORARY TABLE temp1(id int, ans int)
(select qq.id, (
select o.option_id ans 
from question q join question_options o
on(q.id=o.question_id)
where q.id=qq.id
order by rand()
limit 1
) ans from question qq order by qq.id);
) on(q.id=)
select * from temp;
,question q1 
where q1.id=q2.id; 
```

