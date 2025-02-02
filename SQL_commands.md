# SQL Query Examples

## 1. Retrieve Employees with Total Hours Worked and Salary
```sql
SELECT 
    e.EmployeeID,
    e.Name AS EmployeeName,
    d.Name AS DepartmentName,
    COALESCE(SUM(ep.HoursWorked), 0) AS TotalHoursWorked,
    e.Salary
FROM Employees e
LEFT JOIN Departments d ON e.DepartmentID = d.DepartmentID
LEFT JOIN EmployeeProjects ep ON e.EmployeeID = ep.EmployeeID
GROUP BY e.EmployeeID, e.Name, d.Name, e.Salary
ORDER BY TotalHoursWorked DESC, e.Salary DESC;
```

## 2. Projects with Employee Count and Remaining Budget
```sql
SELECT 
    p.ProjectID,
    p.Name AS ProjectName,
    p.Budget - COALESCE(SUM(e.Salary * ep.HoursWorked / 2080), 0) AS RemainingBudget,
    COUNT(DISTINCT ep.EmployeeID) AS TotalEmployees
FROM Projects p
LEFT JOIN EmployeeProjects ep ON p.ProjectID = ep.ProjectID
LEFT JOIN Employees e ON ep.EmployeeID = e.EmployeeID
GROUP BY p.ProjectID, p.Name, p.Budget
HAVING RemainingBudget > 0
ORDER BY RemainingBudget DESC;
```

## 3. Employees Who Worked More Than 100 Hours on a Single Project
```sql
SELECT 
    e.EmployeeID,
    e.Name AS EmployeeName,
    p.Name AS ProjectName,
    ep.HoursWorked
FROM Employees e
INNER JOIN EmployeeProjects ep ON e.EmployeeID = ep.EmployeeID
INNER JOIN Projects p ON ep.ProjectID = p.ProjectID
WHERE ep.HoursWorked > 100
ORDER BY ep.HoursWorked DESC;
```

## 4. Employees Hired in 2021 or Later with No Assigned Department
```sql
SELECT 
    EmployeeID,
    Name AS EmployeeName,
    HireDate
FROM Employees
WHERE HireDate >= '2021-01-01' AND DepartmentID IS NULL
ORDER BY HireDate ASC;
```

## 5. Subquery: Average Salary by Department
```sql
SELECT 
    d.Name AS DepartmentName,
    (SELECT AVG(Salary) FROM Employees WHERE DepartmentID = d.DepartmentID) AS AvgSalary
FROM Departments d
ORDER BY AvgSalary DESC;
```

## 6. Identify Departments with More Than One Employee
```sql
SELECT 
    d.Name AS DepartmentName,
    COUNT(e.EmployeeID) AS EmployeeCount
FROM Departments d
INNER JOIN Employees e ON d.DepartmentID = e.DepartmentID
GROUP BY d.DepartmentID, d.Name
HAVING EmployeeCount > 1;