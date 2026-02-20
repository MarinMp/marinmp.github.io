# PL/SQL Scripts
Se incluirán procedimientos, funciones y triggers desarrollados en PL/SQL para automatizar procesos en bases de datos.

---

## Ejercicios Prácticos en Clase

### Modelo Relacional HR

![MODELO RELACIONAL](/NOSQL/hr-modelo-relacional.png)

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