*2021-03-01*

<https://zhuanlan.zhihu.com/p/250023331>
```
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
 DECLARE p INT;
 SET p = N - 1;
 RETURN (
    SELECT * FROM
     (SELECT DISTINCT Salary FROM Employee
      ORDER BY Salary DESC
      LIMIT 1 OFFSET p
     ) AS getNthHighestSalary);
END;
```
*2020-12-24*

<https://leetcode-cn.com/problems/exchange-seats/>

```
SELECT (CASE 
            WHEN MOD(id,2) = 1 AND id = (SELECT COUNT(*) FROM seat) THEN id
            WHEN MOD(id,2) = 1 THEN id+1
            ElSE id-1
        END) AS id, student
FROM seat
ORDER BY id;
```


LeetCode: Find the Start and End Number of Continuous Ranges
```
select min(log_id) as START_ID, max(log_id) as END_ID
from (select log_id,
        @rank := case when @prev = log_id-1 then @rank else @rank+1 end as rank,
        @prev := log_id as prev
    from Logs,
       (select @rank:=0, @prev:=-1) as rows) as tt
group by rank
order by START_ID
```

#### takeaways: use group by rank


<https://leetcode.com/problems/students-and-examinations/description/>

#### solution1: count with null
```
select Students.student_id, student_name, Subjects.subject_name, count(Examinations.student_id) as attended_exams
from Students join Subjects
left join Examinations
on Students.student_id = Examinations.student_id and Subjects.subject_name = Examinations.subject_name
group by Students.student_id, subject_name
```
#### solution2: use isnull
```
select Students.student_id, student_name, Subjects.subject_name, sum(if(isnull(Examinations.student_id), 0, 1)) as attended_exams
from Students join Subjects
left join Examinations
on Students.student_id = Examinations.student_id and Subjects.subject_name = Examinations.subject_name
group by Students.student_id, subject_name
```

<https://leetcode.com/problems/team-scores-in-football-tournament/>
```
select Teams.team_id, Teams.team_name, sum(if(isnull(num_points), 0, num_points)) as num_points
from Teams left join
    (
        select host_team as team_id,
            sum(case when host_goals>guest_goals then 3 
                     when host_goals=guest_goals then 1
                     else 0 end) as num_points
        from Matches
        group by host_team
        union all
        select guest_team as team_id,
            sum(case when host_goals<guest_goals then 3 
                     when host_goals=guest_goals then 1
                     else 0 end) as num_points
        from Matches
        group by guest_team
    ) as tt
on Teams.team_id = tt.team_id
group by Teams.team_id
order by num_points desc, Teams.team_id asc
```
###### Takeaways:
1. use union all to deal with two parties in one table.
2. left join because some teams don't participate in the match!

*2020-4-2*

<https://leetcode.com/problems/customers-who-bought-all-products/>

#####only need to count amount of product key
```
SELECT customer_id
FROM Customer
GROUP BY customer_id
HAVING COUNT(DISTINCT product_key) = (
    SELECT COUNT(DISTINCT product_key) FROM Product
);
```

<https://leetcode.com/problems/shortest-distance-in-a-plane/>

```
SELECT ROUND(
         SQRT( MIN( POWER(p1.x - p2.x, 2) + POWER(p1.y - p2.y, 2) )
        ), 2) AS shortest
FROM point_2d AS p1 INNER JOIN point_2d AS p2
ON p1.x != p2.x OR p1.y != p2.y --it should be OR here instead of AND
```

*2020-3-30*

<https://leetcode.com/accounts/login/?next=/problems/get-highest-answer-rate-question/>

```
SELECT question_id AS survey_log
FROM survey_log
GROUP BY question_id
ORDER BY SUM(IF(action='answer', 1, 0)) / SUM(IF(action='show', 1, 0)) DESC
LIMIT 0,1;
```

*2020-3-19*

##### How to deal with full out join in mysql: use union all. Notice the difference between Month() and left().

<https://leetcode.com/problems/monthly-transactions-ii/>

```
SELECT month,
       country,
       SUM(IF(type = 'approved', 1, 0)) AS approved_count,
       SUM(IF(type = 'approved', amount, 0)) AS approved_amount,
       SUM(IF(type = 'chargeback', 1, 0)) AS chargeback_count,
       SUM(IF(type = 'chargeback', amount, 0)) AS chargeback_amount
FROM (
        (SELECT LEFT(t.trans_date, 7) AS month,
                t.country,
                amount,
                'approved' AS type
         FROM Transactions AS t
         WHERE state = 'approved' )
      UNION ALL
        (SELECT LEFT(c.trans_date, 7) AS month,
                t.country,
                amount,
                'chargeback' AS type
         FROM Transactions AS t
         INNER JOIN Chargebacks AS c ON t.id = c.trans_id)) AS tt
GROUP BY tt.month,
         tt.country;
```

*2020-3-15*

##### Medium: use average to deal with even amount of numbers
<https://leetcode.com/problems/find-median-given-frequency-of-numbers/>
<https://leetcode.com/problems/median-employee-salary/>

```
SELECT id, company, salary 
FROM Employee e
WHERE 1>=ABS((SELECT COUNT(*) FROM Employee e1 where e.company = e1.comany AND e.salary >= e1.salary)-(SELECT COUNT(*) FROM Employee e2 where e.company = e2.comany AND e.salary <= e2.salary))

SELECT AVG(a.number) median
FROM Numbers a
WHERE a.frequency >= ABS(
    (SELECT SUM(frequency) FROM Numbers WHERE a.number <= number) - 
    (SELECT SUM(frequency) FROM Numbers WHERE a.number >= number))
```

<https://leetcode.com/problems/winning-candidate/>

```
select Name 
from Candidate as Name 
where id =
(select CandidateId from Vote group by CandidateId order by count(CandidateId) desc limit 1);
```

*2020-3-13*

##### get running sum for each group
<https://leetcode.com/problems/game-play-analysis-iii/>

```
right:
SELECT a1.pid, a1.ed,sum(a2.gp) FROM game a1, game a2
where a1.pid=a2.pid and a1.ed >= a2.ed
group by a1.pid, a1.ed;

select a1.player_id, a1.event_date, sum(a2.games_played) as games_played_so_far from game a1
inner join game a2
on a1.player_id = a2.player_id and a1.event_date >= a2.event_date
group by a1.player_id, a1.event_date;

wrong:
SELECT a1.pid, a1.ed,sum(a1.gp) FROM game a1, game a2
where a1.pid=a2.pid and a1.ed =< a2.ed
group by a1.pid, a1.ed;
```


*2020-3-12*

<https://leetcode.com/problems/biggest-single-number/>

```
wrong solution: still have all 1,4,5,6,max not effective because of the group by
SELECT max(num) AS num
FROM number
group by num
having count(num)=1;

right version:
SELECT
    MAX(num) AS num
FROM
    (SELECT
        num
    FROM
        number
    GROUP BY num
    HAVING COUNT(num) = 1) AS t
;
```

<https://leetcode.com/problems/last-person-to-fit-in-the-elevator/>

```
Solution: No subquery
select t1.person_name
from Queue as t1, Queue as t2
where t1.turn >= t2.turn
group by t1.turn
having sum(t2.weight)<=1000
order by sum(t2.weight) desc
limit 1

Solution: subquery with a virtual colum
select t.person_name
from (
    select t1.person_name, sum(t2.weight) as w
    from Queue as t1, Queue as t2
    where t1.turn >= t2.turn
    group by t1.turn
    having w<=1000) as t
order by t.w desc
limit 1
```


*2020-3-11*

<https://leetcode.com/problems/investments-in-2016/>

```
##### solution1
SELECT
    SUM(insurance.TIV_2016) AS TIV_2016
FROM
    insurance
WHERE
    insurance.TIV_2015 IN
    (
      SELECT
        TIV_2015
      FROM
        insurance
      GROUP BY TIV_2015
      HAVING COUNT(*) > 1
    )
    AND CONCAT(LAT, LON) IN
    (
      SELECT
        CONCAT(LAT, LON)
      FROM
        insurance
      GROUP BY LAT , LON
      HAVING COUNT(*) = 1
    )
;

##### solution2
SELECT
    SUM(a.TIV_2016) AS TIV_2016
FROM
    insurance a
WHERE
1=(SELECT COUNT(*) FROM insurance b where a.LAT=b.LAT AND a.LON=b.LON)
1< (SELECT COUNT(*)FROM insurance c where a.TIV_2015=c.TIV_2015)
    
```
###### Takeaways:
1. previous do it wrong by using aggregate funtion count(distinct()) in where
2. can solve this by using (group by + in) or using multiple tables
3. count(LAT,LON)is not correct, can only do that by count(concat(LAT,LON))


*2020-3-9*

<https://leetcode.com/problems/delete-duplicate-emails/>

```
DELETE p1 FROM Person p1,
    Person p2
WHERE
    p1.Email = p2.Email AND p1.Id > p2.Id
;
```

<https://leetcode.com/problems/rank-scores/>

```
SELECT 
    score 
    (SELECT COUNT(distinct score) FROM score WHERE score>=s.score) Rank
FROM score s
ORDER BY score DESC
```

*2020-3-4*

<https://leetcode.com/problems/tree-node/>

```
SELECT
    atree.id,
    IF(ISNULL(atree.p_id),
        'Root',
        IF(atree.id IN (SELECT p_id FROM tree), 'Inner','Leaf')) Type
FROM
    tree atree
;
```
```
SELECT id,
        (
            CASE WHEN p_id IS NULL THEN 'Root'
                 WHEN id IN (SELECT DISTINCT p_id FROM tree) THEN 'Inner'
                 ELSE 'Leaf'
            END
        ) AS Tpye
FROM tree
ORDER BY id;
```

##### Using IF function

<https://leetcode.com/problems/winning-candidate/>
```
SELECT
    name AS 'Name'
FROM
    Candidate
        JOIN
    (SELECT
        Candidateid
    FROM
        Vote
    GROUP BY Candidateid
    ORDER BY COUNT(*) DESC
    LIMIT 1) AS winner
WHERE
    Candidate.id = winner.Candidateid
;
```
###### Takeaways:
1. Two ways to select from multipule table: 
    using where
    use join
2. use group by and order by, don't need to select count()

<https://leetcode.com/problems/friend-requests-i-overall-acceptance-rate/>

```
select
round(
    ifnull(
    (select count(*) from (select distinct requester_id, accepter_id from request_accepted) as A)
    /
    (select count(*) from (select distinct sender_id, send_to_id from friend_request) as B),
    0)
, 2) as accept_rate;
```


*2020-3-2*
<https://leetcode.com/problems/managers-with-at-least-5-direct-reports/>

###### method1 子查询
```
SELECT Name FROM Employee WHERE Id IN
(SELECT ManagerId FROM Employee
 GROUP BY ManagerId
 HAVING COUNT(*) >=5);
```
###### method2 联结
```
SELECT Name FROM Employee AS t1 JOIN
(SELECT ManagerId FROM Employee
 GROUP BY ManagerId
 HAVING COUNT(ManagerId) >= 5) AS t2
ON t1.Id = t2.ManagerId;
```

<https://leetcode.com/problems/consecutive-available-seats/>

```
SELECT C1.seat_id from cinima C1 
WHERE C1.
E=1 AND
(C1.seat_id+1 in (SELECT C1.seat_id from cinima C1 WHERE C1.FREE=1)
OR
C1.seat_id-1 in (SELECT C1.seat_id from cinima C1 WHERE C1.FREE=1))
ORDER BY C1.seat_id 
```

*2020-2-23*

<https://leetcode.com/problems/consecutive-numbers/>

###### method1
```
SELECT DISTINCT a.Num AS ConsecutiveNums
FROM Logs a JOIN Logs b JOIN Logs c ON a.Id = b.Id+1 AND b.Id = c.Id + 1
WHERE a.Num = b.Num AND b.Num = c.Num;
```
###### method2
```
SELECT DISTINCT a.Num AS ConsecutiveNums
FROM Logs a,Logs b,Logs c 
WHERE a.Id = b.Id+1 AND b.Id = c.Id + 1 and a.Num = b.Num AND b.Num = c.Num;
```

###### method3
```
SELECT DISTINCT Num AS ConsecutiveNums FROM (
SELECT Num, 
LEAD(Num,1) OVER(ORDER BY id) AS lead1,
LEAD(Num,2) OVER(ORDER BY id) AS lead2
FROM Logs
) AS lg
WHERE Num = lead1
AND Num = lead2
```



<https://leetcode.com/problems/department-highest-salary/>

```
SELECT D.Name AS Department, e.Name as Employee,E.Salary 
FROM
    Employee E,
    (SELECT DepartmentId,max(Salary) as max FROM Employee GROUP BY DepartmentId) T,
    Department D
WHERE
    E.DepartmentId=T.DepartmentId
AND E.Salary=T.max
AND E.DepartmentId=D.Id
```
