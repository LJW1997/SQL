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
