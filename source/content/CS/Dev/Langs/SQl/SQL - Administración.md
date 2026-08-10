# SQL — Administración general de bases de datos

> Guía de refresco rápido sobre gestión y operación de SGBD relacionales (conceptos transversales a MySQL/MariaDB, PostgreSQL, SQL Server y Oracle). Para sintaxis específica ver [[OracleDB - Administración]] y [[PostgreSQL - Administración]]. Para uso ofensivo (SQLi) ver [[SQL_Cheatsheet]].

#Dev #SQL #DBA

---

## 1. Modelo y conceptos base

| Concepto | Descripción |
|----------|-------------|
| **Instancia** | Proceso(s) del motor + memoria que sirven una o varias bases. |
| **Base de datos / esquema** | Contenedor lógico de objetos (tablas, vistas, índices). En MySQL "schema" = "database"; en PG/Oracle son distintos. |
| **Catálogo / diccionario de datos** | Metadatos del sistema (`information_schema`, `pg_catalog`, vistas `DBA_*`/`USER_*` en Oracle). |
| **Tablespace** | Asociación lógica↔física donde residen los datos (PG/Oracle). |
| **Transacción** | Unidad atómica de trabajo (ver ACID). |
| **DDL / DML / DCL / TCL** | Definición / Manipulación / Control de acceso / Control transaccional. |

**Clasificación de sentencias**

| Grupo | Sentencias | Notas |
|-------|-----------|-------|
| DDL | `CREATE`, `ALTER`, `DROP`, `TRUNCATE` | Suelen hacer *commit* implícito (Oracle/MySQL). |
| DML | `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `MERGE` | Requieren `COMMIT` para persistir (salvo autocommit). |
| DCL | `GRANT`, `REVOKE` | Gestión de privilegios. |
| TCL | `COMMIT`, `ROLLBACK`, `SAVEPOINT` | Control de transacción. |

---

## 2. ACID y transacciones

- **Atomicidad**: todo o nada.
- **Consistencia**: respeta restricciones e invariantes.
- **Aislamiento**: transacciones concurrentes no interfieren (ver niveles).
- **Durabilidad**: lo confirmado sobrevive a caídas (WAL/redo log).

```sql
BEGIN;                       -- o START TRANSACTION
UPDATE cuentas SET saldo = saldo - 100 WHERE id = 1;
UPDATE cuentas SET saldo = saldo + 100 WHERE id = 2;
SAVEPOINT antes_log;
INSERT INTO log VALUES (...);
-- ROLLBACK TO antes_log;    -- deshace solo hasta el savepoint
COMMIT;                      -- o ROLLBACK
```

### Niveles de aislamiento (estándar SQL)

| Nivel | Dirty read | Non-repeatable read | Phantom read |
|-------|-----------|---------------------|--------------|
| READ UNCOMMITTED | Posible | Posible | Posible |
| READ COMMITTED | No | Posible | Posible |
| REPEATABLE READ | No | No | Posible* |
| SERIALIZABLE | No | No | No |

*PostgreSQL en REPEATABLE READ ya evita phantoms (snapshot). Defaults: PG/Oracle = READ COMMITTED, MySQL InnoDB = REPEATABLE READ, SQL Server = READ COMMITTED.

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

---

## 3. Diseño de esquema y restricciones

| Restricción | Función |
|-------------|---------|
| `PRIMARY KEY` | Identificador único + NOT NULL. |
| `FOREIGN KEY` | Integridad referencial entre tablas. |
| `UNIQUE` | Valores no repetidos. |
| `CHECK` | Validación de expresión booleana. |
| `NOT NULL` | Obliga valor. |
| `DEFAULT` | Valor por defecto. |

```sql
CREATE TABLE pedidos (
  id        BIGINT PRIMARY KEY,
  cliente_id BIGINT NOT NULL REFERENCES clientes(id) ON DELETE CASCADE,
  total     NUMERIC(10,2) CHECK (total >= 0),
  estado    VARCHAR(20) DEFAULT 'nuevo',
  creado    TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**Normalización (resumen)**: 1NF (atomicidad de valores) → 2NF (sin dependencias parciales de la PK) → 3NF (sin dependencias transitivas) → BCNF. Desnormalizar de forma controlada solo por rendimiento medido.

---

## 4. Índices

| Tipo | Uso |
|------|-----|
| B-tree | Igualdad y rangos (`=`, `<`, `>`, `BETWEEN`, `ORDER BY`). Default. |
| Hash | Solo igualdad. |
| Compuesto | Varias columnas; importa el orden (regla *leftmost prefix*). |
| Parcial / filtrado | Solo filas que cumplen un `WHERE`. |
| Único | Impone unicidad. |
| Cubriente (covering) | Incluye todas las columnas de la consulta → *index-only scan*. |
| Full-text / GIN / GiST | Texto, JSON, geometría (PG). |

```sql
CREATE INDEX idx_pedidos_cliente ON pedidos(cliente_id, creado DESC);
CREATE UNIQUE INDEX uq_email ON usuarios(lower(email));
DROP INDEX idx_pedidos_cliente;
```

- Indexar columnas de `JOIN`, `WHERE`, `ORDER BY` selectivas.
- Cada índice penaliza `INSERT/UPDATE/DELETE` y consume espacio.
- Una función sobre la columna invalida el índice salvo *index sobre expresión*.

---

## 5. Planes de ejecución y rendimiento

| Acción | Comando |
|--------|---------|
| Plan estimado | `EXPLAIN <query>` |
| Plan + ejecución real | `EXPLAIN ANALYZE <query>` (PG); `EXPLAIN ANALYZE FORMAT=TREE` (MySQL 8.0.18+) |
| Recalcular estadísticas | `ANALYZE` (PG); `ANALYZE TABLE t` (MySQL); `DBMS_STATS` (Oracle) |

**Señales en un plan**
- *Seq Scan / Full Table Scan* sobre tabla grande con filtro selectivo → falta índice.
- Estimaciones de filas muy distintas de las reales → estadísticas desactualizadas.
- *Nested loop* sobre muchas filas → considerar hash/merge join.

**Tácticas de optimización**
- Seleccionar solo columnas necesarias (evitar `SELECT *`).
- Filtrar lo antes posible; evitar funciones sobre columnas indexadas en el `WHERE`.
- Paginación por *keyset* (`WHERE id > :ultimo`) en vez de `OFFSET` grande.
- Sustituir subconsultas correlacionadas por `JOIN` o CTE cuando proceda.
- `EXISTS` suele ganar a `IN` con subconsulta grande.

---

## 6. Vistas, CTE y funciones de ventana

```sql
-- Vista
CREATE VIEW v_activos AS SELECT id, email FROM usuarios WHERE activo;

-- CTE (Common Table Expression)
WITH ventas_mes AS (
  SELECT cliente_id, SUM(total) tot
  FROM pedidos WHERE creado >= date_trunc('month', now())
  GROUP BY cliente_id
)
SELECT * FROM ventas_mes WHERE tot > 1000;

-- Funciones de ventana
SELECT id, total,
       ROW_NUMBER() OVER (PARTITION BY cliente_id ORDER BY creado DESC) AS rn,
       SUM(total)   OVER (PARTITION BY cliente_id) AS total_cliente
FROM pedidos;
```

Una **vista materializada** persiste el resultado (refresco manual o programado): mejora lectura, requiere refresco.

---

## 7. Usuarios, roles y privilegios (DCL)

```sql
CREATE USER app WITH PASSWORD 's3cr3t';     -- sintaxis varía por motor
CREATE ROLE solo_lectura;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO solo_lectura;
GRANT solo_lectura TO app;
REVOKE INSERT ON pedidos FROM app;
```

**Principio de mínimo privilegio**: cuentas de aplicación sin permisos DDL ni `SUPERUSER`. Separar cuentas de lectura, escritura y administración.

| Riesgo | Mitigación |
|--------|-----------|
| Cuenta única todopoderosa | Roles segmentados por función. |
| Contraseñas en claro | Autenticación por certificado/IAM; secretos en gestor (Vault). |
| Privilegios `PUBLIC` | Revocar permisos por defecto sobre esquemas. |

---

## 8. Copias de seguridad y recuperación

| Estrategia | Descripción |
|-----------|-------------|
| **Lógica** | Volcado de sentencias/datos (`mysqldump`, `pg_dump`, `expdp`). Portable, lenta de restaurar. |
| **Física** | Copia de ficheros de datos (`pg_basebackup`, RMAN, snapshots). Rápida, ligada a versión/plataforma. |
| **Completa / incremental / diferencial** | Total vs. solo cambios desde la última completa/incremental. |
| **PITR** (Point-In-Time Recovery) | Backup base + archivado de logs (WAL/redo/binlog) → restaurar a un instante. |

```bash
# Lógica
mysqldump -u root -p --single-transaction db > db.sql
pg_dump -Fc -d db -f db.dump          # formato custom, restaurable selectivo

# Restaurar
mysql -u root -p db < db.sql
pg_restore -d db db.dump
```

- **3-2-1**: 3 copias, 2 medios, 1 fuera de sitio.
- Definir y medir **RPO** (pérdida máx. tolerable) y **RTO** (tiempo máx. de recuperación).
- Un backup no probado no es un backup: **restaurar periódicamente**.

---

## 9. Concurrencia y bloqueos

| Concepto | Descripción |
|----------|-------------|
| **Lock compartido (S)** | Varias lecturas simultáneas. |
| **Lock exclusivo (X)** | Escritura; bloquea a los demás. |
| **MVCC** | Versionado multiversión: lectores no bloquean escritores (PG, Oracle, InnoDB). |
| **Deadlock** | Ciclo de espera mutua; el motor aborta una transacción víctima. |
| **`SELECT ... FOR UPDATE`** | Bloqueo pesimista de filas. |

Evitar deadlocks: acceder a los objetos siempre en el mismo orden, transacciones cortas, índices que reduzcan el rango bloqueado.

---

## 10. Monitorización y mantenimiento

| Área | Qué observar |
|------|--------------|
| Sesiones activas | Consultas largas, bloqueadas, idle-in-transaction. |
| Cache hit ratio | % de lecturas servidas desde memoria. |
| Crecimiento | Tamaño de tablas/índices, *bloat*. |
| Locks | Esperas y deadlocks. |
| Logs | Errores, consultas lentas (*slow query log*). |

Tareas recurrentes: recalcular estadísticas, reconstruir/reorganizar índices fragmentados, purgar versiones muertas (`VACUUM` en PG), rotar logs, revisar espacio en disco.

---

## 11. Seguridad operativa

- Cifrado **en tránsito** (TLS) y **en reposo** (TDE / cifrado de tablespace o de disco).
- **Auditoría** de accesos y cambios DDL/DCL.
- Parámetros sensibles fuera del control de versiones.
- Consultas parametrizadas siempre (defensa frente a [[SQL Injection]]).
- Backups cifrados y con acceso restringido.

---

## 12. Tipos de datos (transversal)

| Categoría | Ejemplos | Nota |
|-----------|----------|------|
| Enteros | `SMALLINT`, `INT`, `BIGINT` | Elegir por rango real. |
| Decimales exactos | `NUMERIC`/`DECIMAL(p,s)` | Dinero: nunca `FLOAT`. |
| Texto | `CHAR(n)`, `VARCHAR(n)`, `TEXT` | `VARCHAR` salvo longitud fija. |
| Fecha/hora | `DATE`, `TIMESTAMP`, `TIMESTAMPTZ` | Guardar en UTC. |
| Booleano | `BOOLEAN` | SQL Server usa `BIT`; Oracle no tiene nativo (<23c). |
| Binario | `BLOB`, `BYTEA` | Ficheros: preferir referencia a objeto externo. |
| Semiestructurado | `JSON`/`JSONB`, `XML` | `JSONB` indexable en PG. |

---

## Recursos

- [Use The Index, Luke! — índices y rendimiento](https://use-the-index-luke.com/)
- [SQL Standard (ISO/IEC 9075) — resumen](https://en.wikipedia.org/wiki/SQL)
- [PostgreSQL docs](https://www.postgresql.org/docs/) · [MySQL docs](https://dev.mysql.com/doc/) · [Oracle docs](https://docs.oracle.com/en/database/)
