# SQL

Se publicarán consultas y ejercicios SQL enfocados en modelado relacional.

---

## Ejercicios Prácticos en Clase

### Modelo Relacional HR

![SQL Screenshot](/SQL/modelo-relacional-hr.png)

---

### Consultas

```sql
-- Autor: Paula Marín
-- Fecha: 06/02/2026
-- Descripción: Traer nombre del empleado, apellido y cantidad de cargos =>2.
 
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
-- Autor: Paula Marín
-- Fecha: 06/02/2026
-- Descripción: Traer el nombre del empleado, país y salario de empleados que vivan o trabajen en Europa y que ganen entre 7000 y 9000 dólares.
 
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
-- Autor: Paula Marín
-- Fecha: 06/02/2026
-- Descripción: Traer empleados con sus managers.
 
SELECT E.FIRST_NAME || ' ' || E.LAST_NAME AS NOMBRE_EMPLEADO,  
       M.FIRST_NAME || ' ' || M.LAST_NAME NOMBRE_MANAGER
FROM HR.EMPLOYEES E  
     LEFT JOIN HR.EMPLOYEES M  
       ON E.MANAGER_ID = M.EMPLOYEE_ID
ORDER BY E.FIRST_NAME DESC;
```

```sql
-- Autor: Paula Marín
-- Fecha: 09/02/2026
-- Descripción: Copiar la tabla de 'EMPLOYEES' donde los empleados pertenezan al departamento 80 y 90.
 
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