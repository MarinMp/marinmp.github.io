# PL/SQL Scripts
Se incluirán procedimientos, funciones y triggers desarrollados en PL/SQL para automatizar procesos en bases de datos.

---

## Ejercicios Prácticos en Clase

### Modelo Relacional HR

![MODELO RELACIONAL](/PLSQL/hr-modelo-relacional.png)

---

### Consultas
```sql
-- Bloque anónimo
-- Fecha: 06/02/2026
-- Descripción: Obtiene la fecha y hora actual del sistema, la guarda en una variable y la muestra en pantalla.

DECLARE
    fecha timestamp;
BEGIN
    SELECT sysdate INTO fecha FROM dual;
    dbms_output.put_line('La fecha es: ' || fecha);
END;
```