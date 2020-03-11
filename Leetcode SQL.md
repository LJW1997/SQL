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

```
SELECT
    Name
FROM
    Employee AS t1 JOIN
    (SELECT
        ManagerId
    FROM
        Employee
    GROUP BY ManagerId
    HAVING COUNT(ManagerId) >= 5) AS t2
    ON t1.Id = t2.ManagerId
;
```

<https://leetcode.com/problems/consecutive-available-seats/>

```
SELECT C1.seat_id from cinima C1 
WHERE C1.FREE=1 AND
(C1.seat_id+1 in (SELECT C1.seat_id from cinima C1 WHERE C1.FREE=1)
OR
C1.seat_id-1 in (SELECT C1.seat_id from cinima C1 WHERE C1.FREE=1))
ORDER BY C1.seat_id 
```

*2020-2-23*

<https://leetcode.com/problems/consecutive-numbers/>

```
SELECT DISTINCT a.Num AS ConsecutiveNums
FROM Logs a JOIN Logs b JOIN Logs c ON a.Id = b.Id+1 AND b.Id = c.Id + 1
WHERE a.Num = b.Num AND b.Num = c.Num;
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
