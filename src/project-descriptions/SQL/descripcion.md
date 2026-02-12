# Proyecto SQL Avanzado

Este proyecto demuestra consultas avanzadas en bases de datos relacionales utilizando SQL.

---

## Descripción

El objetivo fue diseñar y consultar una base de datos académica que permite:

- Registrar estudiantes
- Registrar cursos
- Relacionar estudiantes con cursos
- Generar reportes estadísticos

---

## Imagen del Proyecto

![SQL Screenshot](/src/assets/SQL/SQL.png)

---

## Consulta Principal

```sql
SELECT students.name, courses.course_name
FROM students
JOIN enrollments ON students.id = enrollments.student_id
JOIN courses ON enrollments.course_id = courses.id
WHERE courses.active = 1;
