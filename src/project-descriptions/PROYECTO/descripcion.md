# Proyecto Final
Proyecto final de Base de Datos II.

Grupo: David Ortiz - Diego Rodríguez - Paula Marín.

---

## 🥃 Distilled Records – Sistema de Información y Venta

**Distilled Records** es una aplicación diseñada para la **gestión integral de una licorera**, permitiendo registrar y controlar productos, inventarios, proveedores, clientes y ventas de manera centralizada y eficiente.  
El sistema implementa lógica de negocio directamente en la base de datos mediante procedimientos almacenados, triggers y funciones, integra MongoDB para datos complementarios no relacionales, y expone una API REST construida en Spring Boot.

---

## Modelo Entidad Relación Extendido - Peter Chen

![MERE-CHEN](/PROYECTO/MERE_PeterChen.png)

---

## Modelo Entidad Relación - Barker

![MER-BARKER](/PROYECTO/MER_Barker.png)

---

## Modelo Relacional - Barker

![MR-BARKER](/PROYECTO/MR_Barker.png)

--

## Implementación de la Base de Datos Relacional

```sql
-- Autores: David Ortiz, Diego Rodríguez, Paula Marín.
-- Descripción: Crea la base de datos relacional de Distilled Records con sus tablas, relaciones y restricciones principales.

DROP DATABASE IF EXISTS LICORERA;

CREATE DATABASE LICORERA;

USE LICORERA;

SET FOREIGN_KEY_CHECKS = 0;

DROP TABLE IF EXISTS DETALLE_VENTA;
DROP TABLE IF EXISTS VENTA;
DROP TABLE IF EXISTS PRODUCTO;
DROP TABLE IF EXISTS INVENTARIO;
DROP TABLE IF EXISTS CATEGORIA;
DROP TABLE IF EXISTS PROVEEDOR;
DROP TABLE IF EXISTS EMPLEADO;
DROP TABLE IF EXISTS CLIENTE;
DROP TABLE IF EXISTS USUARIO;
DROP TABLE IF EXISTS CIUDAD;

SET FOREIGN_KEY_CHECKS = 1;

CREATE TABLE CIUDAD (
  ID_CIUDAD INT AUTO_INCREMENT PRIMARY KEY,
  NOMBRE VARCHAR(60) NOT NULL UNIQUE
);

CREATE TABLE USUARIO (
  ID_USUARIO VARCHAR(50) PRIMARY KEY,
  NOMBRE VARCHAR(60) NOT NULL,
  NUM_TELEFONO VARCHAR(20) NOT NULL UNIQUE
);

CREATE TABLE CLIENTE (
  ID_CLIENTE VARCHAR(15) PRIMARY KEY,
  ID_USUARIO VARCHAR(50) NOT NULL UNIQUE,    
  CORREO VARCHAR(120) NOT NULL UNIQUE,

  CONSTRAINT fk_cliente_usuario
    FOREIGN KEY (ID_USUARIO) REFERENCES USUARIO(ID_USUARIO)
);

CREATE TABLE EMPLEADO (
  ID_EMPLEADO VARCHAR(15) PRIMARY KEY,
  ID_USUARIO VARCHAR(50) NOT NULL UNIQUE,       
  USER_NAME VARCHAR(50) NOT NULL UNIQUE,

  CONSTRAINT fk_empleado_usuario
    FOREIGN KEY (ID_USUARIO) REFERENCES USUARIO(ID_USUARIO)
);

CREATE TABLE PROVEEDOR (
  ID_PROVEEDOR VARCHAR(15) PRIMARY KEY,
  ID_USUARIO VARCHAR(50) NOT NULL UNIQUE,    
  CORREO VARCHAR(120) NOT NULL UNIQUE,
  DIRECCION VARCHAR(200) NOT NULL,
  ID_CIUDAD INT NOT NULL,
  CONSTRAINT fk_proveedor_usuario
    FOREIGN KEY (ID_USUARIO) REFERENCES USUARIO(ID_USUARIO),
  CONSTRAINT fk_proveedor_ciudad
    FOREIGN KEY (ID_CIUDAD) REFERENCES CIUDAD(ID_CIUDAD)
);

CREATE TABLE CATEGORIA (
    ID_CATEGORIA INT AUTO_INCREMENT PRIMARY KEY,
    NOMBRE VARCHAR(100),
    DESCRIPCION VARCHAR(200)
);

CREATE TABLE INVENTARIO (
    ID_INVENTARIO INT AUTO_INCREMENT PRIMARY KEY,
    CANTIDAD_DISPONIBLE INT
);

CREATE TABLE PRODUCTO (
    ID_PRODUCTO INT AUTO_INCREMENT PRIMARY KEY,
    MARCA VARCHAR(100),
    PRECIO INT,
    GRADO_ALCOHOL INT,
    ID_PROVEEDOR VARCHAR(15),
    ID_CATEGORIA INT,
    ID_INVENTARIO INT,
    CONSTRAINT FK_PRODUCTO_PROVEEDOR 
        FOREIGN KEY (ID_PROVEEDOR) REFERENCES PROVEEDOR(ID_PROVEEDOR),
    CONSTRAINT FK_PRODUCTO_CATEGORIA 
        FOREIGN KEY (ID_CATEGORIA) REFERENCES CATEGORIA(ID_CATEGORIA),
    CONSTRAINT FK_PRODUCTO_INVENTARIO 
        FOREIGN KEY (ID_INVENTARIO) REFERENCES INVENTARIO(ID_INVENTARIO)
);

CREATE TABLE VENTA (
    ID_VENTA INT AUTO_INCREMENT PRIMARY KEY,
    MONTO DECIMAL(10,2),
    FECHA DATE,
    ID_CLIENTE VARCHAR(15),
    ID_EMPLEADO VARCHAR(15),
    CONSTRAINT FK_VENTA_CLIENTE 
        FOREIGN KEY (ID_CLIENTE) REFERENCES CLIENTE(ID_CLIENTE),
    CONSTRAINT FK_VENTA_EMPLEADO 
        FOREIGN KEY (ID_EMPLEADO) REFERENCES EMPLEADO(ID_EMPLEADO)
);

CREATE TABLE DETALLE_VENTA (
    ID_DETALLE INT AUTO_INCREMENT PRIMARY KEY,
    CANTIDAD_PRODUCTOS INT,
    SUBTOTAL DECIMAL(10,2),
    IVA DECIMAL(10,2),
    ID_VENTA INT,
    ID_PRODUCTO INT,
    CONSTRAINT FK_DETALLE_VENTA_VENTA 
        FOREIGN KEY (ID_VENTA) REFERENCES VENTA(ID_VENTA),
    CONSTRAINT FK_DETALLE_VENTA_PRODUCTO 
        FOREIGN KEY (ID_PRODUCTO) REFERENCES PRODUCTO(ID_PRODUCTO)
);
```

```sql
-- Autores: David Ortiz, Diego Rodríguez, Paula Marín.
-- Descripción: Implementa funciones, triggers y procedimientos para la gestión de ventas, validación de stock, auditoría y control transaccional en la base de datos LICORERA.

CREATE TABLE IF NOT EXISTS AUDITORIA_VENTA (
  ID_AUDITORIA INT AUTO_INCREMENT PRIMARY KEY,
  ID_VENTA INT,
  ACCION VARCHAR(50),
  FECHA_EVENTO TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  USUARIO_BD VARCHAR(100) DEFAULT (CURRENT_USER())
);

-- Funcion: Calcular subtotal
DELIMITER $$
CREATE FUNCTION fn_subtotal(precio INT, cantidad INT)
RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
  RETURN ROUND(precio * cantidad, 2);
END$$
DELIMITER ;

-- Funcion: Calcular IVA
DELIMITER $$
CREATE FUNCTION fn_calcular_iva(subtotal DECIMAL(10,2))
RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
  RETURN ROUND(subtotal * 0.19, 2);
END$$
DELIMITER ;

-- Trigger: Validar Stock
DELIMITER $$
CREATE TRIGGER trg_detalle_bi_validar
BEFORE INSERT ON DETALLE_VENTA
FOR EACH ROW
BEGIN
  DECLARE v_stock INT;

  IF NEW.CANTIDAD_PRODUCTOS <= 0 THEN
    SIGNAL SQLSTATE '45000'
    SET MESSAGE_TEXT = 'Cantidad inválida';
  END IF;

  SELECT i.CANTIDAD_DISPONIBLE INTO v_stock
  FROM PRODUCTO p
  JOIN INVENTARIO i ON p.ID_INVENTARIO = i.ID_INVENTARIO
  WHERE p.ID_PRODUCTO = NEW.ID_PRODUCTO;

  IF v_stock < NEW.CANTIDAD_PRODUCTOS THEN
    SIGNAL SQLSTATE '45000'
    SET MESSAGE_TEXT = 'Stock insuficiente';
  END IF;
END$$
DELIMITER ;

-- Trigger: Actualizar Inventario + Auditoria
DELIMITER $$
CREATE TRIGGER trg_detalle_ai_update
AFTER INSERT ON DETALLE_VENTA
FOR EACH ROW
BEGIN
  -- Descontar inventario
  UPDATE INVENTARIO i
  JOIN PRODUCTO p ON p.ID_INVENTARIO = i.ID_INVENTARIO
  SET i.CANTIDAD_DISPONIBLE = i.CANTIDAD_DISPONIBLE - NEW.CANTIDAD_PRODUCTOS
  WHERE p.ID_PRODUCTO = NEW.ID_PRODUCTO;

  -- Auditoría
  INSERT INTO AUDITORIA_VENTA (ID_VENTA, ACCION)
  VALUES (NEW.ID_VENTA, 'DETALLE REGISTRADO Y STOCK ACTUALIZADO');
END$$
DELIMITER ;

-- Trigger: Auditoria de Venta Directa
DELIMITER $$
CREATE TRIGGER trg_venta_ai_auditoria
AFTER INSERT ON VENTA
FOR EACH ROW
BEGIN
  INSERT INTO AUDITORIA_VENTA (ID_VENTA, ACCION)
  VALUES (NEW.ID_VENTA, 'VENTA CREADA');
END$$
DELIMITER ;

-- Procedimiento: Transajes + Auditoria
DELIMITER $$
CREATE PROCEDURE sp_registrar_venta_simple(
  IN p_cliente VARCHAR(15),
  IN p_empleado VARCHAR(15),
  IN p_producto INT,
  IN p_cantidad INT,
  IN p_fecha DATE
)
BEGIN
  DECLARE v_id_venta INT;
  DECLARE v_precio INT;
  DECLARE v_subtotal DECIMAL(10,2);
  DECLARE v_iva DECIMAL(10,2);

  -- Manejo de errores
  DECLARE EXIT HANDLER FOR SQLEXCEPTION
  BEGIN
    -- Auditoría de error
    INSERT INTO AUDITORIA_VENTA (ID_VENTA, ACCION)
    VALUES (NULL, 'ERROR EN TRANSACCION DE VENTA');

    ROLLBACK;
  END;

  START TRANSACTION;

  -- Crear venta
  INSERT INTO VENTA (MONTO, FECHA, ID_CLIENTE, ID_EMPLEADO)
  VALUES (0, p_fecha, p_cliente, p_empleado);

  SET v_id_venta = LAST_INSERT_ID();

  -- Obtener precio
  SELECT PRECIO INTO v_precio
  FROM PRODUCTO
  WHERE ID_PRODUCTO = p_producto;

  IF v_precio IS NULL THEN
    SIGNAL SQLSTATE '45000'
    SET MESSAGE_TEXT = 'Producto no existe';
  END IF;

  -- Calcular valores
  SET v_subtotal = fn_subtotal(v_precio, p_cantidad);
  SET v_iva = fn_calcular_iva(v_subtotal);

  -- Insertar detalle
  INSERT INTO DETALLE_VENTA
  (CANTIDAD_PRODUCTOS, SUBTOTAL, IVA, ID_VENTA, ID_PRODUCTO)
  VALUES (p_cantidad, v_subtotal, v_iva, v_id_venta, p_producto);

  -- Actualizar monto total
  UPDATE VENTA
  SET MONTO = (
    SELECT SUM(SUBTOTAL + IVA)
    FROM DETALLE_VENTA
    WHERE ID_VENTA = v_id_venta
  )
  WHERE ID_VENTA = v_id_venta;

  -- Auditoría exitosa
  INSERT INTO AUDITORIA_VENTA (ID_VENTA, ACCION)
  VALUES (v_id_venta, 'VENTA COMPLETA EXITOSA');

  COMMIT;
END$$
DELIMITER ;
```
--

## Implementación MongoDB

```sql
-- Autores: David Ortiz, David Rodríguez, Paula Marín.
-- Descripción: Implementa estructuras NoSQL para el almacenamiento de datos complementarios, auditoría, sesiones, alertas de inventario y reportes del sistema Distilled Records.

-- PASO 1: Seleccionar base de datos
use distilled_records_nosql

-- PASO 2: logs_actividad 
db.logs_actividad.insertOne({
  timestamp: new Date(),
  usuario: {
    id_mysql: "EMP001",
    username: "cajero1",
    rol: "CAJERO"
  },
  accion: {
    modulo: "VENTAS",
    operacion: "REGISTRAR_VENTA",
    descripcion: "Venta registrada exitosamente"
  },
  contexto: {
    id_referencia_mysql: 105,
    tabla_mysql: "VENTA",
    ip: "192.168.1.20"
  },
  resultado: "EXITOSO",
  duracion_ms: 312
})

-- PASO 3: sesiones_usuario
db.sesiones_usuario.insertOne({
  id_empleado_mysql: "EMP001",
  username: "cajero1",
  token: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9",
  creado_en: new Date(),
  expira_en: new Date(new Date().getTime() + 8*60*60*1000),
  ip_origen: "192.168.1.20",
  activa: true,
  ultimo_acceso: new Date(),
  modulos_visitados: ["VENTAS", "INVENTARIO", "REPORTES"]
})

-- PASO 4: alertas_inventario 
db.alertas_inventario.insertOne({
  id_producto_mysql: 12,
  nombre_producto: "Glenfiddich 12 Years 700ml",
  tipo_alerta: "STOCK_BAJO",
  stock_actual: 3,
  stock_minimo: 10,
  unidades_faltantes: 7,
  proveedor: {
    id_mysql: "PROV001",
    nombre: "Licores del Valle S.A.S",
    telefono: "3001234567"
  },
  fecha_alerta: new Date(),
  estado: "PENDIENTE",
  atendida_por: null
})

-- PASO 5: reportes_cache
db.reportes_cache.insertOne({
  tipo_reporte: "VENTAS_DIARIAS",
  parametros: { fecha_inicio: "2026-05-01", fecha_fin: "2026-05-18" },
  generado_por: "admin",
  generado_en: new Date(),
  expira_en: new Date(new Date().getTime() + 12*60*60*1000),
  resultado: {
    total_ventas: 45,
    monto_total: 12750000,
    producto_mas_vendido: "Glenfiddich 12 Years",
    empleado_top: "cajero1",
    detalle_por_dia: [
      { fecha: "2026-05-01", ventas: 3, monto: 850000 },
      { fecha: "2026-05-02", ventas: 5, monto: 1200000 }
    ]
  }
})

-- PASO 6: Verificar colecciones
show collections
db.logs_actividad.find()
db.sesiones_usuario.find()
db.alertas_inventario.find()
db.reportes_cache.find()

-- PASO 7: Agregaciones 

-- 7.1 Top usuarios con más acciones
db.logs_actividad.aggregate([
  { $match: { resultado: "EXITOSO" } },
  { $group: {
      _id: "$usuario.username",
      total_acciones: { $sum: 1 },
      modulos: { $addToSet: "$accion.modulo" }
  }},
  { $sort: { total_acciones: -1 } },
  { $limit: 5 }
])

-- 7.2 Productos con stock bajo por proveedor
db.alertas_inventario.aggregate([
  { $match: { estado: "PENDIENTE" } },
  { $group: {
      _id: "$proveedor.nombre",
      productos_alertados: { $sum: 1 },
      unidades_faltantes: { $sum: "$unidades_faltantes" },
      productos: { $push: "$nombre_producto" }
  }},
  { $sort: { productos_alertados: -1 } }
])

-- 7.3 Actividad del sistema por módulo y hora
db.logs_actividad.aggregate([
  { $addFields: { hora: { $hour: "$timestamp" } } },
  { $group: {
      _id: { modulo: "$accion.modulo", hora: "$hora" },
      cantidad: { $sum: 1 },
      duracion_promedio_ms: { $avg: "$duracion_ms" }
  }},
  { $sort: { "_id.hora": 1 } }
])

-- 7.4 Reportes generados por usuario
db.reportes_cache.aggregate([
  { $group: {
      _id: "$generado_por",
      total_reportes: { $sum: 1 },
      tipos: { $addToSet: "$tipo_reporte" }
  }},
  { $sort: { total_reportes: -1 } }
])

```