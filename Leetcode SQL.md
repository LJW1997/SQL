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
C1.seat_id+1 in (SELECT C1.seat_id from cinima C1 WHERE C1.FREE=1))
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
