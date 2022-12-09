### 题目网址

[牛客SQL编程练习](https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199)

### 非技术快速入门

### 1. 基础查询

#### SQL1. 查询所有列

```sql
SELECT * FROM user_profile
```

#### SQL2. 查询多列

```mysql
SELECT device_id, gender, age, university FROM user_profile
```

#### SQL3. 查询结果去重

- 使用**distinct**关键字 , distinct要放到列名的前面
- 使用**group by**关键字

```mysql
1. select distinct university from user_profile
2. select university from user_profile group by university
```

#### SQL4. **查询结果限制返回行数**

- 使用limit关键字 
  - limit m,n：从m+1条数据开始，取n条数据
  - limit m：从第1条数据开始，取m条

```mysql
1. select device_id from user_profile limit 0,2
2. select device_id from user_profile limit 2
```

####  SQL5.**将查询后的列重新命名**

- 使用**as**关键词，**as**可省略

```mysql
select device_id as user_infos_example from user_profile limit 0,2
```



### 2. 条件查询

#### 2.1 基础排序

#### SQL36. 查找后排序

- 使用**order by**关键字
- **asc**为升序，**desc**为降序，**缺省为升序**

```mysql
select device_id,age from user_profile order by age asc
```

#### SQL37. **查找后多列排序**

```mysql
select device_id,gpa,age from user_profile order by gpa, age
```

#### SQL38. 查找后降序排列

- 每个字段后都需要**desc**

```mysql
select device_id,gpa,age from user_profile order by gpa desc,age desc
```



#### 2.2基础操作符

#### SQL6. 查找学校是北大的学生信息

- **where**关键字
- **like**关键字——模糊查询，**%为通配符**

```mysql
select device_id,university from user_profile where university='北京大学'
select device_id,university from user_profile where university like '北%'
select device_id,university from user_profile where university like '北京大学'
```

#### SQL7. 查找年龄大于24岁的用户信息

```mysql
select device_id, gender, age, university from user_profile where age>24
```

#### SQL8. 查找某个年龄段的用户信息

- 使用**between**关键字，需注意的是，between关键字为**左闭右闭**，因此使用的时候要注意条件

```mysql
select device_id, gender, age from user_profile where age>=20 and age<=23
select device_id, gender, age from user_profile where age between 20 and 23
```

#### SQL9. 查找除复旦大学的用户信息

- **not like，**使用**not**表否定
- **!=**  /  **<>**

```mysql
select device_id, gender, age, university from user_profile where university not like '复旦大学'
select device_id, gender, age, university from user_profile where university !=  '复旦大学'
```

#### SQL10. 用where过滤空值练习

- 第一种写法中的**null**需**添加引号**
- 注意**is [not] null**是一个关键词，是一个整体，而**is不是mysql中的关键字**

```mysql
select device_id, gender, age, university from user_profile where age != 'null'
select device_id, gender, age, university from user_profile where age is not null
```



#### 2.3. 高级操作符

#### SQL11. 高级操作符练习(1)

```mysql
select device_id, gender, age, university, gpa from user_profile where gender='male' and gpa>3.5
```



### 3. 高级查询

#### 3.1 计算函数

#### SQL16. 查找GPA最高值

- 使用聚合函数**max()**求最大值

```mysql
select max(gpa) from user_profile where university like "复%"
```

- **倒序第一条**即为最高值

```mysql
select gpa from user_profile where university like "复%" order by gpa desc limit 0,1
```

#### SQL17. 计算男生人数以及平均GPA

- **count**()函数：用于统计个数，返回不为空值的个数
- **avg()**函数：用于求平均值
- **round()**函数：用于四舍五入，其中的第二个参数为保留几位小数；如**round(6,5599,1) = 6.6**

```mysql
select count(gender) as male_num, round(avg(gpa),1) as avg_gpa from user_profile where gender='male'
```



#### 3.2 分组查询

#### SQL18.  分组计算练习题

- **group by**关键字：用于分组查询

```mysql
select 
gender,
university,
count(gender) as user_num,
round(avg(active_days_within_30),1) as avg_active_day,round(avg(question_cnt),1) as avg_question_cnt from user_profile group by gender, university
```

#### SQL19. 分组过滤练习题

- **having**关键字
  - having用于group by**之后**(针对**分组后的数据**进行筛选，**group by要比having先执行**)，where用于group by之前
  - **聚合函数**结果作为筛选条件时，不能用where，而是用having，where是先筛选后聚合，having是先聚合后筛选
  - having通常与group by一起使用
  - group by后面不可能跟聚合函数
- 给列起的别名，可以直接在having语句中使用，涉及到**关键词执行先后问题**

```mysql
select 
university,
round(avg(question_cnt),3) as avg_question_cnt,
round(avg(answer_cnt),3) as avg_answer_cnt
from user_profile group by university having avg_question_cnt<5 or avg_answer_cnt<20
```

#### SQL20. 分组排序练习题

```mysql
select 
university,
round(avg(question_cnt),4) as avg_question_cnt 
from user_profile 
group by university order by avg_question_cnt asc
```



### 4. 多表查询

#### 4.1 子查询

#### SQL21. 浙江大学用户题目回答情况

- **子查询**：where语句后面嵌套select查询

```mysql
select device_id,question_id,result from question_practice_detail where device_id =(
    select device_id from user_profile where university="浙江大学") order by question_id
```

- **连接查询：给表起别名**   **join关键字**
  - inner join：内连接
  - left join：左外连接
  - right join：右外连接

```mysql
select a.device_id, a.question_id, a.result from question_practice_detail a, user_profile b where university='浙江大学' and a.device_id = b.device_id order by question_id

select u.device_id, q.question_id, q.result from question_practice_detail q inner join user_profile u on q.device_id=u.device_id where university='浙江大学'
```



#### 4.2 链接查询

#### SQL22. 统计每个学校的答过题的用户的平均答题数

 ```mysql

 ```

​	