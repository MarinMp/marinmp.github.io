# PL/SQL Scripts
Se incluirán procedimientos, funciones y triggers desarrollados en PL/SQL para automatizar procesos en bases de datos.

---

## Ejercicios Prácticos en Clase

### Modelo Relacional HR

![MODELO RELACIONAL](/PLSQL/hr-modelo-relacional.png)

---

### Consultas

#### Bloques anónimos

```sql
-- Bloque anónimo
-- Fecha: 13/02/2026
-- Descripción: Obtiene la fecha y hora actual del sistema y la muestra en pantalla.

DECLARE
    fecha timestamp;
BEGIN
    SELECT sysdate INTO fecha FROM dual;

    DBMS_OUTPUT.PUT_LINE('La fecha es: ' || fecha);
END;
```

```sql
-- Bloque anónimo
-- Fecha: 13/02/2026
-- Descripción: Obtiene el salario del empleado con ID 109 utilizando %TYPE y lo muestra en pantalla.

DECLARE 
    v_salario HR.EMPLOYEES.SALARY%TYPE;
BEGIN
    SELECT SALARY INTO  v_salario
    FROM HR.EMPLOYEES
    WHERE EMPLOYEE_ID = 109;

    DBMS_OUTPUT.PUT_LINE('El salario es: ' || v_salario);
END;
```

```sql
-- Bloque anónimo
-- Fecha: 13/02/2026
-- Descripción: Obtiene el nombre, salario del empleado con ID 109 y su fecha de contratación utilizando %TYPE y lo muestra en pantalla.

DECLARE 
    v_fecha_contratacion HR.EMPLOYEES.HIRE_DATE%TYPE;
    v_salario HR.EMPLOYEES.SALARY%TYPE;
    v_nombre HR.EMPLOYEES.FIRST_NAME%TYPE;
BEGIN
    SELECT SALARY, FIRST_NAME, HIRE_DATE INTO v_salario, v_nombre, v_fecha_contratacion
    FROM HR.EMPLOYEES
    WHERE EMPLOYEE_ID = 109;

    DBMS_OUTPUT.PUT_LINE('El nombre del empleado es: ' || v_nombre || ', su salario es: ' || v_salario || ' y la fecha de contratación es: ' || v_fecha_contratacion);
END;
```

```sql
-- Bloque anónimo
-- Fecha: 13/02/2026
-- Descripción: Obtiene el nombre, salario del empleado con ID 109 y su fecha de contratación utilizando %ROWTYPE y lo muestra en pantalla.

DECLARE 
    v_empleado HR.EMPLOYEES%ROWTYPE;
BEGIN
    SELECT * INTO v_empleado
    FROM HR.EMPLOYEES
    WHERE EMPLOYEE_ID = 109;

    DBMS_OUTPUT.PUT_LINE('El nombre del empleado es: ' || v_empleado.FIRST_NAME || ', su salario es: ' || v_empleado.SALARY || ' y la fecha de contratación es: ' || v_empleado.HIRE_DATE);
END;
```


#### Procedimientos almacenados (SP) 