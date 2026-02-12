# SQL

Se publicarán consultas y ejercicios SQL enfocados en modelado relacional.

---

## Ejercicios Prácticos en Clase

### Modelo Relacional HR

![SQL Screenshot](/SQL/modelo-relacional-hr.png)

---

## Consulta Principal

```sql
/*
Autor: Paula Marín
Fecha: 06/02/2026
Descripción: Traer empleados con sus managers
*/
 
SELECT E.FIRST_NAME || ' ' || E.LAST_NAME AS NOMBRE_EMPLEADO,  
       M.FIRST_NAME || ' ' || M.LAST_NAME NOMBRE_MANAGER
FROM HR.EMPLOYEES E  
LEFT JOIN HR.EMPLOYEES M  
ON E.MANAGER_ID = M.EMPLOYEE_ID
ORDER BY E.FIRST_NAME DESC;
