# SQL

Se publicarán consultas y ejercicios SQL enfocados en modelado relacional.

---

## Ejercicios Prácticos en Clase

### Modelo Relacional HR

![MODELO RELACIONAL](/SQL/hr-modelo-relacional.png)

---

### Consultas

```sql
-- Autor: Paula Marín
-- Fecha: 06/02/2026
-- Descripción: Trae el nombre del empleado, apellido y cantidad de cargos =>2.
 
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
-- Descripción: Trae el nombre del empleado, país y salario de empleados que vivan o trabajen en Europa y que ganen entre 7000 y 9000 dólares.
 
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
-- Descripción: Trae empleados con sus managers.
 
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
-- Descripción: Copia la tabla de 'EMPLOYEES' donde los empleados pertenezan al departamento 80 y 90.
 
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

```sql
-- Autor: Paula Marín
-- Fecha: 16/02/2026
-- Descripción: Crea una tabla PAIS.
 
 CREATE TABLE PAIS(
    COD_PAIS NUMBER PRIMARY KEY,
    NOM_PAIS VARCHAR2(100)
);
```

```sql
-- Autor: Paula Marín
-- Fecha: 16/02/2026
-- Descripción: Crea una tabla EQUIPO.
 
CREATE TABLE EQUIPO(
    COD_EQUIPO NUMBER PRIMARY KEY,
    NOM_EQUIPO VARCHAR2(100),
    COD_PAIS NUMBER
);
```

```sql
-- Autor: Paula Marín
-- Fecha: 16/02/2026
-- Descripción: Agrega una clave foránea en la tabla EQUIPO que vincula COD_PAIS con PAIS(COD_PAIS) y la activa.

ALTER TABLE  EQUIPO ADD CONSTRAINT "EQUI_PA_FK" FOREIGN KEY ("COD_PAIS")
      REFERENCES PAIS ("COD_PAIS") ENABLE;
```
