# SQL
Queries and SQL exercises focused on relational modeling.

---

## Practical Class Exercises

### HR Relational Model

![SQL Screenshot](/SQL/modelo-relacional-hr.png)

---

### Queries

```sql
-- Author: Paula Marín
-- Date: 06/02/2026
-- Description: Bring employee first name, last name and number of positions ≥ 2.
 
SELECT E.FIRST_NAME, 
       E.EMPLOYEE_ID,
       COUNT (J.JOB_ID)+1 
FROM HR.EMPLOYEES E 
     JOIN HR.JOB_HISTORY J 
       ON E.EMPLOYEE_ID = J.EMPLOYEE_ID 
GROUP BY E.FIRST_NAME, 
         E.EMPLOYEE_ID 
HAVING COUNT (J.JOB_ID)+1 >= 2; 
```

```sql
-- Author: Paula Marín
-- Date: 06/02/2026
-- Description: Bring employee name, country and salary of employees who live or work in Europe and earn between 7000 and 9000 dollars.
 
SELECT E.FIRST_NAME,
       E.LAST_NAME,
       C.COUNTRY_NAME,
       E.SALARY
FROM HR.EMPLOYEES E
     JOIN HR.DEPARTMENTS D
       ON E.DEPARTMENT_ID = D.DEPARTMENT_ID
     JOIN HR.LOCATIONS L
       ON D.LOCATION_ID = L.LOCATION_ID
     JOIN HR.COUNTRIES C
       ON L.COUNTRY_ID = C.COUNTRY_ID
     JOIN HR.REGIONS R
       ON C.REGION_ID = R.REGION_ID
WHERE R.REGION_NAME = 'Europe'
      AND E.SALARY BETWEEN 7000 AND 9000; 
```

```sql
-- Author: Paula Marín
-- Date: 06/02/2026
-- Description: Bring employees with their managers.
 
SELECT E.FIRST_NAME || ' ' || E.LAST_NAME AS NOMBRE_EMPLEADO,  
       M.FIRST_NAME || ' ' || M.LAST_NAME NOMBRE_MANAGER
FROM HR.EMPLOYEES E  
     LEFT JOIN HR.EMPLOYEES M  
       ON E.MANAGER_ID = M.EMPLOYEE_ID
ORDER BY E.FIRST_NAME DESC;
```

```sql
-- Author: Paula Marín
-- Date: 09/02/2026
-- Description: Copy the EMPLOYEES table where employees belong to departments 80 and 90.
 
CREATE TABLE EMPLEADO AS
SELECT E.FIRST_NAME,
       E.LAST_NAME,
       C.COUNTRY_NAME,
       E.SALARY,
       D.DEPARTMENT_NAME
FROM HR.EMPLOYEES E
     INNER JOIN HR.DEPARTMENTS D
       ON E.DEPARTMENT_ID = D.DEPARTMENT_ID
     INNER JOIN HR.LOCATIONS L
       ON D.LOCATION_ID = L.LOCATION_ID
     INNER JOIN HR.COUNTRIES C
       ON L.COUNTRY_ID = C.COUNTRY_ID
WHERE D.DEPARTMENT_ID IN (80, 90);
```
