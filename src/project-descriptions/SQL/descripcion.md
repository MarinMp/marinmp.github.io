# Evidencia SQL – Consultas Avanzadas

Este proyecto contiene consultas avanzadas utilizando JOIN, GROUP BY y subconsultas para análisis de datos académicos.

---

## 📌 Objetivo

Demostrar el uso correcto de:
- INNER JOIN
- LEFT JOIN
- Subconsultas
- Funciones de agregación

---

## 🧠 Ejemplo de consulta

```sql
SELECT e.nombre, COUNT(m.id_materia) AS total_materias
FROM estudiantes e
INNER JOIN matriculas m ON e.id = m.id_estudiante
GROUP BY e.nombre;
