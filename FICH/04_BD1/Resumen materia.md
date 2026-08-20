
## U1 · Sistemas de Manejo de Bases de Datos (DBMS)

Un **DBMS** es un conjunto de datos relacionados más los programas para gestionarlos. Su objetivo es guardar y recuperar información de forma conveniente, segura y eficiente.

**Enfoques de diseño de sistemas** (de menor a mayor conveniencia actual):

- Orientado al **procedimiento** (AyD clásico)
- Orientado a **datos** (AyD conceptual)
- Orientado a **objetos** ← más conveniente para proyectos nuevos

### Abstracción de la información

| Nivel $\downarrow$ | Descripción                                                      |
| ------------------ | ---------------------------------------------------------------- |
| **Físico**         | Cómo se almacenan los datos en disco                             |
| **Conceptual**     | Qué datos existen y cómo se relacionan (visión lógica unificada) |
| **De visión**      | Cómo ven los datos los usuarios o aplicaciones                   |

**Independencia de datos:** capacidad de modificar un nivel sin afectar el inmediato superior.

- _Física:_ cambios en almacenamiento no obligan a reescribir aplicaciones.
- _Lógica:_ cambios conceptuales no obligan a reescribir aplicaciones (más difícil de lograr).

### Lenguajes SQL

|Componente|Función|Comandos clave|
|---|---|---|
|**DDL** (Definición)|Estructura de la BD|`CREATE`, `ALTER`, `DROP`|
|**DML** (Manipulación)|Datos en las tablas|`SELECT`, `INSERT`, `UPDATE`, `DELETE`|
|**DCL** (Control)|Permisos y seguridad|`GRANT`, `REVOKE`|

### Tipos de datos principales

```sql
-- Cadenas
CHAR, CHARACTER VARYING

-- Numéricos
INTEGER, SMALLINT, DECIMAL

-- Fecha
DATE
```

### Ventajas y desventajas de los DBMS

**Ventajas:** independencia datos/programas, acceso y consulta facilitados, concurrencia, seguridad, sistemas evolutivos.  
**Desventajas:** mayor uso de hardware, menor velocidad, incompatibilidad entre DBMS, más canales I/O.

---

## U2 · La Base de Datos Relacional (RDBMS)

### Conceptos fundamentales

- **Entidad:** representación de una porción del mundo real (población).
- **Relación:** vínculo entre individuos de la misma o distintas poblaciones.
- **Clave primaria (PK):** identifica unívocamente a un individuo.
- **Clave foránea (FK):** actúa como puntero lógico a la PK de otra tabla.

### Tipos de relaciones

```
1-1:  Persona ←→ Pasaporte (FK UNIQUE en una de las tablas)
1-N:  Cliente ←→ Pedido   (FK en el lado N)
M-N:  Estudiante ←→ Materia (tabla intermedia con dos FK)
```

### Características de las tablas
- Todas las filas son únicas (1FN).
- El orden de filas y columnas es indiferente.
- Cada celda contiene un único valor.
- **Tipos:** base (datos reales), temporales (solo en sesión), virtuales/vistas (derivadas de una consulta).

### NULL
Representa información desconocida o no disponible. Es distinto de `0` y de cadena vacía. Ningún `NULL` es igual a otro.

### Principios del RDBMS (Codd)
- Toda información se representa como valores en tablas.
- Cada dato es accesible por: nombre de tabla + nombre de columna + valor de PK.
- Los valores nulos se manejan de forma consistente.
- Debe existir un lenguaje completo de manipulación de datos.
- Las operaciones de modificación deben poder aplicarse en conjunto (no solo fila a fila).
- Los cambios físicos o lógicos no afectan a programas ni usuarios.

---

## U3 · SQL

### DDL — Definición de estructura

```sql
CREATE TABLE cliente (
    id        BIGINT PRIMARY KEY,
    nombre    VARCHAR(100) NOT NULL,
    email     VARCHAR(100) UNIQUE
);

ALTER TABLE cliente ADD COLUMN telefono VARCHAR(20);

DROP TABLE cliente;
```

**Constraints disponibles:** `DEFAULT`, `CHECK`, `UNIQUE`, `PRIMARY KEY`, `REFERENCES` (FK).

### Índices
Aceleran el acceso reduciendo operaciones de disco. Funcionan como el índice de un libro.

|Tipo|Descripción|
|---|---|
|**Clustered**|Ordena físicamente la tabla por la clave (máx. 1 por tabla)|
|**Nonclustered**|Estructura separada; no altera el orden físico (máx. 249)|

> Las PK y UNIQUE generan índices implícitamente. Las FK **no**: crearlos manualmente mejora el rendimiento en JOINs y operaciones de integridad referencial.

**Índice filtrado:** índice sobre un subconjunto de datos según una condición.

```sql
CREATE UNIQUE INDEX persona_fisica_cuil_ipk ON persona.persona_fisica (cuil)
WHERE cuil IS NOT NULL;
```

### Transacciones y ACID
Una transacción es una unidad atómica de instrucciones SQL: o se completan todas o no se aplica ninguna.

|Propiedad|Significado|
|---|---|
|**Atomicidad**|Todo o nada|
|**Consistencia**|Solo transiciones entre estados válidos|
|**Aislamiento**|Las transacciones concurrentes son independientes|
|**Durabilidad**|Los cambios persisten ante fallas|

```sql
BEGIN;
  UPDATE cuenta SET saldo = saldo - 100 WHERE id = 1;
  UPDATE cuenta SET saldo = saldo + 100 WHERE id = 2;
COMMIT;   -- o ROLLBACK si algo falla
```

---

## U4 · SELECT

### Estructura básica

```sql
SELECT columnas
FROM tabla
[WHERE condición]
[GROUP BY columnas]
[HAVING condición_de_grupo]
[ORDER BY columnas ASC|DESC];
```

### Predicados

|Predicado|Ejemplo|
|---|---|
|Comparación|`WHERE codigo > 1000`|
|Rango|`WHERE fecha BETWEEN '2000-01-01' AND '2005-01-01'`|
|Conjunto|`WHERE tipodoc IN ('LE', 'LC')`|
|Patrón|`WHERE apenom LIKE 'S%'`|
|Nulo|`WHERE domicilio IS NULL`|

### Funciones agregadas

```sql
COUNT(*), SUM(col), AVG(col), MAX(col), MIN(col)

-- Uso típico con GROUP BY y HAVING:
SELECT producto, SUM(cantidad) AS total
FROM ventas
GROUP BY producto
HAVING SUM(cantidad) > 100;
```

### JOINs

```sql
-- Solo filas con coincidencia en ambas tablas
INNER JOIN tabla2 ON tabla1.id = tabla2.fk

-- Todas las filas de la izquierda + coincidencias de la derecha (NULL si no hay)
LEFT  JOIN tabla2 ON ...

-- Todas las filas de la derecha + coincidencias de la izquierda
RIGHT JOIN tabla2 ON ...

-- Todas las filas de ambas tablas
FULL  JOIN tabla2 ON ...

-- Producto cartesiano (todas las combinaciones posibles)
CROSS JOIN tabla2
```

### Operadores de conjuntos

```sql
-- Filas presentes en ambas consultas
SELECT ... INTERSECT SELECT ...

-- Filas en la primera consulta que no están en la segunda
SELECT ... EXCEPT SELECT ...
```

### Subconsultas

```sql
-- En WHERE
SELECT nombre FROM empleados
WHERE salario > (SELECT AVG(salario) FROM empleados);

-- Con EXISTS
SELECT * FROM cliente c
WHERE EXISTS (SELECT 1 FROM factura f WHERE f.id_cliente = c.id);
```

### Information Schema

Vistas estándar para consultar metadatos de la BD:

|Vista|Contenido|
|---|---|
|`INFORMATION_SCHEMA.TABLES`|Tablas de la BD|
|`INFORMATION_SCHEMA.COLUMNS`|Columnas y tipos de datos|
|`INFORMATION_SCHEMA.TABLE_CONSTRAINTS`|PK, FK, restricciones UNIQUE|
|`INFORMATION_SCHEMA.VIEWS`|Vistas definidas|

---

## U5 · Programación PL/pgSQL

### Procedimientos almacenados vs Funciones

||Procedimiento|Función|
|---|---|---|
|Retorna valor|No|Sí (escalar o tabla)|
|Uso en `SELECT`|No|Sí|
|Maneja transacciones|Sí|No|
|Comando de creación|`CREATE PROCEDURE`|`CREATE FUNCTION`|
|Ejecución|`CALL nombre(...)`|`SELECT nombre(...)`|

### Procedimiento básico

```sql
CREATE OR REPLACE PROCEDURE agregar_cliente(
    nombre_cliente VARCHAR,
    email_cliente  VARCHAR
)
LANGUAGE plpgsql AS $$
BEGIN
    INSERT INTO persona.persona (email) VALUES (email_cliente);
END;
$$;

CALL agregar_cliente('Juan Perez', 'juan@example.com');
```

### Función básica

```sql
CREATE OR REPLACE FUNCTION total_ventas_cliente(id_cliente BIGINT)
RETURNS NUMERIC
LANGUAGE plpgsql AS $$
DECLARE total NUMERIC;
BEGIN
    SELECT SUM(f.total) INTO total
    FROM venta.factura f
    WHERE f.id_cliente = id_cliente;
    RETURN COALESCE(total, 0);
END;
$$;

SELECT total_ventas_cliente(1);
```

### Volatilidad de funciones

|Categoría|Puede leer BD|Puede modificar BD|Resultado garantizado|
|---|---|---|---|
|`IMMUTABLE`|No|No|Siempre el mismo para mismos args|
|`STABLE`|Sí|No|Constante dentro de la misma consulta|
|`VOLATILE` (default)|Sí|Sí|Sin garantías|

### Elementos de programación

**Variables y bloques anónimos (DO):**

```sql
DO $$
DECLARE v_id BIGINT;
BEGIN
    SELECT id INTO v_id FROM persona.provincia WHERE codigo = 1;
    RAISE NOTICE 'ID: %', v_id;
END;
$$;
```

**Estructuras de control:**

```sql
-- IF
IF condicion THEN ...
ELSIF otra THEN ...
ELSE ...
END IF;

-- FOR (recorre filas de una consulta)
FOR registro IN SELECT id, precio FROM producto LOOP
    -- lógica por fila
END LOOP;

-- WHILE
WHILE condicion LOOP ... END LOOP;
```

**Secuencias:**

```sql
CREATE SEQUENCE persona.persona_sequence START WITH 1 INCREMENT BY 1;

-- Uso:
nextval('secuencia')   -- siguiente valor
currval('secuencia')   -- valor actual (sin incrementar)
setval('secuencia', n) -- fijar valor manualmente
```

> Alternativa simplificada: tipos `SERIAL` (integer) y `BIGSERIAL` (bigint).

**Cursores** (solo cuando la lógica fila a fila es inevitable; preferir operaciones SET):

```sql
DECLARE cur CURSOR FOR SELECT id, total FROM venta.factura;
OPEN cur;
LOOP
    FETCH cur INTO v_id, v_total;
    EXIT WHEN NOT FOUND;
    -- procesar fila
END LOOP;
CLOSE cur;
```

### Triggers

Se ejecutan automáticamente ante `INSERT`, `UPDATE` o `DELETE`. Usos principales: auditoría, integridad referencial automática, recálculo de totales.

```sql
-- 1. Función que ejecuta el trigger
CREATE OR REPLACE FUNCTION actualizar_total_factura()
RETURNS TRIGGER AS $$
BEGIN
    UPDATE venta.factura
    SET total = (
        SELECT SUM(cantidad * precio_unitario)
        FROM venta.factura_detalle
        WHERE id_factura = NEW.id_factura
    )
    WHERE id = NEW.id_factura;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- 2. Asociar el trigger a la tabla
CREATE TRIGGER trg_total_factura
AFTER INSERT OR UPDATE OR DELETE ON venta.factura_detalle
FOR EACH ROW EXECUTE FUNCTION actualizar_total_factura();
```

- `NEW` → valores después de la operación.
- `OLD` → valores antes de la operación.
- `FOR EACH ROW` → ejecuta por cada fila afectada.
- `FOR EACH STATEMENT` → ejecuta una sola vez por sentencia (mejor para operaciones masivas).

**Buenas prácticas con triggers:** evitar recursividad, mantener lógica breve, no usarlos para toda la lógica de negocio, documentarlos bien.

### Vistas

```sql
-- Vista normal (no almacena datos, siempre refleja el estado actual)
CREATE VIEW venta.v_ventas_activas AS
SELECT id_cliente, total FROM venta.factura WHERE estado = 'OK';

-- Vista materializada (almacena datos físicamente, requiere refresco manual)
CREATE MATERIALIZED VIEW venta.mv_ventas_mensuales AS
SELECT EXTRACT(YEAR FROM fecha) AS anio,
       EXTRACT(MONTH FROM fecha) AS mes,
       SUM(total) AS total_mes
FROM venta.factura GROUP BY anio, mes;

-- Refrescar:
REFRESH MATERIALIZED VIEW venta.mv_ventas_mensuales;

-- Refrescar sin bloquear lecturas (requiere índice único):
REFRESH MATERIALIZED VIEW CONCURRENTLY venta.mv_ventas_mensuales;
```

### Manipulación masiva y SQL dinámico

```sql
-- Inserción masiva desde otra tabla
INSERT INTO producto.marca (id, descripcion)
SELECT nextval('seq'), pp.marca
FROM producto.proveedor_precio pp
WHERE NOT EXISTS (SELECT 1 FROM producto.marca m WHERE m.descripcion = pp.marca);

-- Actualización en lote
UPDATE producto.producto AS p
SET costo_unitario = pp.precio
FROM producto.proveedor_precio pp
JOIN producto.marca m ON pp.marca = m.descripcion
WHERE p.id_marca = m.id AND pp.producto = p.descripcion;

-- Borrado masivo
DELETE FROM producto.producto AS p
WHERE NOT EXISTS (
    SELECT 1 FROM venta.factura_detalle fd WHERE fd.id_producto = p.id
);
```

**SQL dinámico** (construir consultas en tiempo de ejecución):

```sql
CREATE OR REPLACE FUNCTION sql_dinamico(nom_tabla VARCHAR, nom_columna VARCHAR)
RETURNS SETOF BIGINT AS $$
DECLARE v_cadena VARCHAR;
BEGIN
    v_cadena := 'SELECT ' || nom_columna || ' FROM ' || nom_tabla;
    RETURN QUERY EXECUTE v_cadena;
END;
$$ LANGUAGE plpgsql;
```

> ⚠️ Riesgo de **SQL injection** si se concatenan entradas del usuario sin validar. Preferir parámetros y encapsular en procedimientos.

### Concurrencia y niveles de aislamiento

|Nivel|Lee no confirmados|Non-repeatable read|Phantom read|
|---|---|---|---|
|Read Uncommitted|✅ (dirty read)|✅|✅|
|**Read Committed** (default)|❌|✅|✅|
|Repeatable Read|❌|❌|✅|
|**Serializable**|❌|❌|❌|

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
BEGIN;
-- operaciones
COMMIT;
```

**Bloqueo optimista:** usa un campo `version`; si cambió antes de actualizar, hay conflicto. Eficiente para lectura intensiva con pocas colisiones.  
**Bloqueo pesimista:** usa `SELECT ... FOR UPDATE`; bloquea la fila desde la lectura. Apropiado para escrituras frecuentes y datos críticos.

---

## U5 (conceptual) · Modelo Conceptual (MDC)

### Elementos del modelo

|Elemento|Descripción|
|---|---|
|**Entidad**|Objeto del mundo real con atributos que lo caracterizan|
|**Relación**|Asociación entre entidades; puede tener propiedades propias|
|**Propiedad**|Atributo que caracteriza una entidad o relación|
|**Dominio**|Conjunto de reglas de validación y valores posibles de un atributo|

**Tipos de entidades/relaciones:**

- _Permanentes:_ estructurales, siempre presentes.
- _De movimiento:_ registran eventos.

**Propiedades según naturaleza:**

- _Elemental:_ no se puede descomponer.
- _Concatenada:_ se puede descomponer en otras.

**Propiedades según persistencia:**

- _Memorizada:_ valor no deducible.
- _Calculada:_ se obtiene por cálculo.

### Cardinalidad y funcionalidad

- **Funcionalidad:** 1-1, 1-N, M-N.
- **Cardinalidad mínima:** 0 (participación opcional) o 1 (obligatoria).
- **Cardinalidad máxima:** 1 o N.

### Dependencias funcionales y normalización

**Dependencia funcional (DF):** $X \rightarrow Y$ — los valores de $X$ determinan unívocamente los valores de $Y$.

- **DFE (elemental):** $Y$ es un atributo simple.
- **DFED (elemental directa):** no hay transitividad.

|Forma Normal|Requisito|
|---|---|
|**1FN**|Propiedades elementales, clave definida, un valor por celda|
|**2FN**|1FN + todas las propiedades dependen de la clave completa (DFE)|
|**3FN**|2FN + dependencias directas sin transitividad (DFED)|
|**BCNF**|En claves concatenadas, ningún componente depende de otra propiedad|
|**4FN**|BCNF + sin dependencias de valores múltiples|
|**5FN**|4FN + toda dependencia join es consecuencia de las claves candidatas|

### MDC Extendido

|Abstracción|Descripción|
|---|---|
|**Clasificación**|Define un concepto como conjunto de objetos con propiedades comunes|
|**Agregación**|Nuevo concepto formado por componentes (entidad débil)|
|**Generalización**|Herencia; relación de subconjunto con coberturas: total/parcial × exclusiva/superpuesta|

---

## U6 · DBA y Aspectos Internos de las BBDD

### Entornos de desarrollo

```
Desarrollo → Prueba/QA → Producción
```

Cada entorno debe parecerse lo más posible al de producción.

### Roles de usuario

|Rol|Descripción|
|---|---|
|NAIF|Usuario final sin conocimientos técnicos|
|OCASIONAL|Conoce SQL, realiza consultas breves|
|SOFISTICADO|Analista que consulta sin programar|
|DESARROLLADOR|Escribe programas que interactúan con la BD|
|DE APLICACIÓN|Usuario de conexión de una app, permisos mínimos|
|DISEÑADOR|Genera el modelo conceptual hasta el físico|
|**DBA**|Administra, mantiene, optimiza y asegura la BD|
|SYSADMIN|Gestiona el SO y hardware para el DBMS|

### Funciones del DBA

- Instalación y configuración del servidor.
- Gestión de usuarios, roles y permisos.
- Modificación de estructura y organización física.
- Copias de seguridad y restauraciones.
- Supervisión del rendimiento y tunning.

### Configuración principal de PostgreSQL

**`postgresql.conf`** — parámetros del motor:

```ini
shared_buffers = 4GB
max_connections = 200
maintenance_work_mem = 512MB
```

**`pg_hba.conf`** — control de acceso (quién se conecta, desde dónde, con qué autenticación):

```
# TYPE   DATABASE   USER      ADDRESS         METHOD
local    all        all                       trust
host     postgres   jpereira  127.0.0.1/32    md5
```

### Tablespaces

```sql
CREATE TABLESPACE ventas_datos LOCATION '/opt/data';
ALTER TABLE articulos TABLESPACE ventas_datos;
```

### Roles y permisos

```sql
CREATE ROLE nombre WITH LOGIN PASSWORD 'pass' VALID UNTIL '2026-01-01';
CREATE USER davide WITH PASSWORD 'pass';

GRANT SELECT, INSERT ON TABLE cliente TO nombre;
REVOKE INSERT ON TABLE cliente FROM nombre;
```

### Copias de seguridad

|Tipo|Qué copia|Restauración|
|---|---|---|
|**Full**|Todo|Solo el backup completo|
|**Diferencial**|Cambios desde el último full|Full + último diferencial|
|**Incremental**|Cambios desde el último backup|Full + todos los incrementales posteriores|

---

## U7 · Data Warehousing e Inteligencia de Negocios

### Conceptos clave

|Término|Definición|
|---|---|
|**BI**|Proceso completo: datos → información → conocimiento → sabiduría → decisiones|
|**DWH**|Proceso de extracción, transformación y carga de datos para análisis|
|**DW**|Base de datos multidimensional, integrada, no volátil, histórica|
|**Data Mart**|DW restringido a un área o problema específico|
|**Granularidad**|Nivel de detalle del dato; más detalle = más posibilidades analíticas (irreversible)|

### Proceso ETL

```
Data Sources
    └─ Extracción → Staging Area (copia cruda)
         └─ Transformación (limpieza, calidad, normalización)
              └─ Carga → DW / DM
```

### Estructura del DW

**Tabla de Hechos:** contiene los eventos a analizar. Su PK es la concatenación de las claves de las dimensiones relacionadas. Puede tener hechos básicos (columnas directas) y derivados (calculados).

**Tabla de Dimensiones:** contexto del negocio (quién, qué, cuándo, dónde). Tienen clave subrogada (numérica secuencial, sin significado de negocio), claves ajenas y atributos descriptivos.

### Modelos multidimensionales

|Esquema|Descripción|
|---|---|
|**Star**|Una tabla de hechos + dimensiones directamente relacionadas. Simple y rápido.|
|**Snowflake**|Extensión del star; dimensiones organizadas en jerarquías. Más normalizado.|
|**Starflake**|Combinación de varios esquemas estrella con hechos principales y auxiliares.|

### Operaciones analíticas (Query Manager)

|Operación|Descripción|
|---|---|
|**Drill-down**|De resumen a detalle (año → trimestre → mes)|
|**Drill-up**|De detalle a resumen|
|**Drill-across**|Consulta entre cubos que comparten dimensiones|
|**Pivot**|Cambio de orientación en la visualización|
|**Drill-through**|Ver los datos de máximo detalle detrás de un indicador|

### SCD — Dimensiones que cambian lentamente

|Tipo|Estrategia|Historial|
|---|---|---|
|**0**|No registrar cambios|No|
|**1**|Sobrescribir el valor|No|
|**2**|Nueva fila con fechas de vigencia|Completo ← más usado|
|**3**|Nueva columna con valor anterior|Limitado (solo el previo)|
|**4**|Historial en tabla separada|Completo en tabla aparte|
|**6**|Híbrido (1+2+3)|Completo + columna de valor actual|

### Dimensiones especiales

- **Realizadoras de roles:** misma dimensión física usada con distintos roles (ej. Fecha de Venta, Fecha de Envío).
- **No deseadas / Junk:** agrupan atributos sueltos (códigos, indicadores binarios) que no encajan en otra dimensión.
- **Degeneradas:** atributo dimensional almacenado directamente en la tabla de hechos (sin tabla propia); ocurre cuando el elemento es un ID sin más atributos.
- **Tablas de hechos sin hechos:** solo claves de dimensiones, sin medidas; modelan eventos o registros de presencia.

---

## Repaso general — Proceso de software y ciclo de datos

**Proceso de software:**

1. _Especificación_ — clientes y desarrolladores definen funcionalidades y restricciones.
2. _Diseño e implementación_ — se programa cumpliendo las especificaciones.
3. _Validación_ — se verifica que el software cumple los requisitos.
4. _Evolución_ — se adapta a nuevos requerimientos.

**Ciclo de vida del dato:** $$\text{Dato} \rightarrow \text{Información} \rightarrow \text{Conocimiento} \rightarrow \text{Sabiduría}$$

**OLTP vs OLAP:**

| |OLTP|OLAP|
|---|---|---|
|Orientado a|Transacciones diarias|Análisis histórico|
|Registros|Muchos, pequeños|Grandes volúmenes|
|Prioridad|Velocidad|Consultas complejas|
