# Oracle Database — Administración

> Refresco rápido de operación y administración de Oracle Database (11g–23ai). Conceptos transversales en [[SQL - Administración]]. Comparativa con Postgres en [[PostgreSQL - Administración]].

#Dev #SQL #Oracle #DBA

---

## 1. Arquitectura

| Componente | Descripción |
|-----------|-------------|
| **Instancia** | SGA (memoria compartida) + procesos en segundo plano. |
| **Base de datos** | Ficheros: datafiles, control files, redo logs. |
| **CDB / PDB** | Multitenant (12c+): Container DB (CDB) que aloja Pluggable DBs (PDB). |
| **SGA** | Memoria global: Buffer Cache, Shared Pool, Redo Log Buffer, Large Pool. |
| **PGA** | Memoria privada por sesión (sort, hash). |
| **Procesos clave** | `SMON` (recuperación), `PMON`/`CLMN` (limpieza), `DBWn` (escritura buffers), `LGWR` (redo), `CKPT` (checkpoint), `ARCn` (archivado). |
| **Tablespace** | Agrupación lógica de datafiles. Estándar: `SYSTEM`, `SYSAUX`, `TEMP`, `UNDO`, `USERS`. |
| **Schema** | Conjunto de objetos de un usuario; en Oracle usuario ≈ schema. |

```sql
SELECT name, cdb, open_mode FROM v$database;
SHOW PDBS;                              -- en SQL*Plus (CDB)
SELECT instance_name, status FROM v$instance;
```

---

## 2. Conexión: SQL*Plus, SQLcl y descriptores

```bash
sqlplus usuario/clave@//host:1521/SERVICIO
sqlplus / as sysdba                     # autenticación de SO
sqlplus sys/clave@//host:1521/CDB as sysdba
sql usuario/clave@servicio              # SQLcl (cliente moderno, autocompletado)
```

**Descriptor EZConnect**: `//host:puerto/servicio`. Alternativa: alias en `tnsnames.ora`.

| Variable | Función |
|----------|---------|
| `ORACLE_HOME` | Instalación del software. |
| `ORACLE_SID` | Identificador de la instancia local. |
| `TNS_ADMIN` | Ruta de `tnsnames.ora` / `sqlnet.ora`. |

```sql
-- Cambio de contenedor dentro de una CDB
ALTER SESSION SET CONTAINER = pdb1;
```

---

## 3. Arranque / parada de la instancia

```sql
-- Conectado as sysdba
STARTUP;                  -- NOMOUNT -> MOUNT -> OPEN
STARTUP MOUNT;            -- para recuperación / cambios de modo
ALTER DATABASE OPEN;
SHUTDOWN IMMEDIATE;       -- recomendado: deshace transacciones y cierra
SHUTDOWN ABORT;           -- forzado, sin commit; requiere recuperación al arrancar

-- PDBs
ALTER PLUGGABLE DATABASE pdb1 OPEN;
ALTER PLUGGABLE DATABASE pdb1 CLOSE IMMEDIATE;
ALTER PLUGGABLE DATABASE ALL OPEN;
```

| Modo SHUTDOWN | Comportamiento |
|---------------|----------------|
| `NORMAL` | Espera a que se desconecten todas las sesiones. |
| `TRANSACTIONAL` | Espera a que terminen las transacciones. |
| `IMMEDIATE` | Rollback de transacciones activas, cierre limpio. |
| `ABORT` | Corte inmediato; recuperación de instancia al reiniciar. |

---

## 4. Usuarios, perfiles y privilegios

```sql
-- En 12c+ los usuarios locales de PDB no llevan prefijo; los comunes sí: C##nombre
CREATE USER app IDENTIFIED BY "Clave#2024"
  DEFAULT TABLESPACE users
  TEMPORARY TABLESPACE temp
  QUOTA 500M ON users;

GRANT CREATE SESSION, CREATE TABLE, CREATE VIEW TO app;
GRANT SELECT, INSERT, UPDATE ON ventas.pedidos TO app;
GRANT solo_lectura TO app;              -- rol

ALTER USER app QUOTA UNLIMITED ON users;
ALTER USER app ACCOUNT LOCK;            -- bloquear
ALTER USER app PASSWORD EXPIRE;

REVOKE CREATE TABLE FROM app;
DROP USER app CASCADE;                  -- elimina objetos del schema
```

| Tipo de privilegio | Ejemplos |
|--------------------|----------|
| Sistema | `CREATE SESSION`, `CREATE TABLE`, `CREATE ANY TABLE`, `SYSDBA`. |
| Objeto | `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `EXECUTE` sobre objeto concreto. |
| Roles predefinidos | `CONNECT`, `RESOURCE`, `DBA` (evitar `DBA` en cuentas de app). |

**Perfiles** (límites de recurso y contraseña):

```sql
CREATE PROFILE app_prof LIMIT
  FAILED_LOGIN_ATTEMPTS 5
  PASSWORD_LIFE_TIME 90
  SESSIONS_PER_USER 10
  IDLE_TIME 30;
ALTER USER app PROFILE app_prof;
```

---

## 5. Tablespaces y almacenamiento

```sql
CREATE TABLESPACE datos
  DATAFILE '/u01/oradata/datos01.dbf' SIZE 200M
  AUTOEXTEND ON NEXT 50M MAXSIZE 5G;

ALTER TABLESPACE datos ADD DATAFILE '/u01/oradata/datos02.dbf' SIZE 200M;
ALTER DATABASE DATAFILE '/u01/oradata/datos01.dbf' RESIZE 400M;
ALTER TABLESPACE datos OFFLINE;
DROP TABLESPACE datos INCLUDING CONTENTS AND DATAFILES;
```

**Uso de espacio**

```sql
SELECT tablespace_name,
       ROUND(used_space*8192/1024/1024) used_mb,    -- depende del block size
       ROUND(tablespace_size*8192/1024/1024) total_mb
FROM   dba_tablespace_usage_metrics;

SELECT segment_name, bytes/1024/1024 mb
FROM   dba_segments WHERE owner='APP' ORDER BY bytes DESC;
```

---

## 6. Diccionario de datos y vistas dinámicas

| Prefijo | Alcance |
|---------|---------|
| `USER_*` | Objetos propiedad del usuario actual. |
| `ALL_*` | Objetos accesibles por el usuario. |
| `DBA_*` | Todos los objetos (requiere privilegio). |
| `V$*` / `GV$*` | Vistas dinámicas de rendimiento (instancia / global RAC). |

```sql
SELECT table_name FROM user_tables;
SELECT * FROM dba_users;
SELECT * FROM dba_data_files;
SELECT * FROM v$session WHERE status='ACTIVE';
SELECT * FROM v$sql ORDER BY elapsed_time DESC FETCH FIRST 10 ROWS ONLY;
DESC dba_objects;                       -- describe estructura (SQL*Plus)
```

---

## 7. Sesiones, bloqueos y procesos

```sql
-- Sesiones y a quién bloquean
SELECT sid, serial#, username, status, sql_id, blocking_session
FROM   v$session WHERE username IS NOT NULL;

-- Matar una sesión
ALTER SYSTEM KILL SESSION '145,67' IMMEDIATE;   -- 'sid,serial#'

-- Bloqueos
SELECT * FROM v$lock WHERE block = 1;
SELECT * FROM dba_blockers;
```

---

## 8. Transacciones y deshacer (UNDO)

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;   -- default: READ COMMITTED
SAVEPOINT s1;
ROLLBACK TO s1;
COMMIT;
```

- **UNDO tablespace**: guarda imágenes previas → consistencia de lectura (MVCC) y rollback.
- `UNDO_RETENTION` controla cuánto se conservan; relacionado con *Flashback*.
- Error frecuente: `ORA-01555: snapshot too old` → UNDO insuficiente para una consulta larga.

**Flashback** (recuperación lógica sin restaurar backup):

```sql
SELECT * FROM pedidos AS OF TIMESTAMP (SYSTIMESTAMP - INTERVAL '10' MINUTE);
FLASHBACK TABLE pedidos TO TIMESTAMP (SYSTIMESTAMP - INTERVAL '1' HOUR);
FLASHBACK TABLE pedidos TO BEFORE DROP;          -- recupera de la papelera
```

---

## 9. Backup y recuperación (RMAN + Data Pump)

### RMAN (físico)

```bash
rman target /
```
```rman
BACKUP DATABASE PLUS ARCHIVELOG;
BACKUP TABLESPACE users;
BACKUP INCREMENTAL LEVEL 1 DATABASE;
LIST BACKUP SUMMARY;
CROSSCHECK BACKUP;
DELETE OBSOLETE;                         -- según RETENTION POLICY
-- Recuperación
RESTORE DATABASE;
RECOVER DATABASE;
```

Requiere **ARCHIVELOG** para backups en caliente y PITR:

```sql
SELECT log_mode FROM v$database;
SHUTDOWN IMMEDIATE; STARTUP MOUNT;
ALTER DATABASE ARCHIVELOG;
ALTER DATABASE OPEN;
```

### Data Pump (lógico — `expdp`/`impdp`)

```bash
expdp app/clave DIRECTORY=dpdir DUMPFILE=app.dmp LOGFILE=app.log SCHEMAS=app
impdp app/clave DIRECTORY=dpdir DUMPFILE=app.dmp REMAP_SCHEMA=app:app2
```
```sql
CREATE DIRECTORY dpdir AS '/u01/dpdump';
GRANT READ, WRITE ON DIRECTORY dpdir TO app;
```

| Herramienta | Tipo | Uso típico |
|-------------|------|-----------|
| RMAN | Física, a nivel de bloque | DR completo, PITR, incrementales. |
| Data Pump | Lógica, por objeto/schema | Migración, clonado de esquema, traspasos. |

---

## 10. Rendimiento y estadísticas

```sql
-- Estadísticas del optimizador (CBO)
EXEC DBMS_STATS.GATHER_SCHEMA_STATS('APP');
EXEC DBMS_STATS.GATHER_TABLE_STATS('APP','PEDIDOS');

-- Plan de ejecución
EXPLAIN PLAN FOR SELECT * FROM pedidos WHERE cliente_id = 5;
SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);

-- Plan real ejecutado
SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY_CURSOR(:sql_id));

-- Trazado y autotrace
SET AUTOTRACE ON;                        -- SQL*Plus: muestra plan + estadísticas
```

| Herramienta | Función |
|-------------|---------|
| **AWR** (Automatic Workload Repository) | Snapshots de rendimiento; informe histórico. Requiere Diagnostics Pack. |
| **ASH** (Active Session History) | Muestreo de sesiones activas. |
| **ADDM** | Diagnóstico automático con recomendaciones. |
| **SQL Tuning Advisor** | Sugiere índices/perfiles para un SQL. |
| Hints | `/*+ INDEX(t idx) */`, `/*+ FULL(t) */`, `/*+ PARALLEL(t,4) */`. |

```sql
SELECT * FROM TABLE(DBMS_WORKLOAD_REPOSITORY.AWR_REPORT_TEXT(...));  -- o @?/rdbms/admin/awrrpt
```

> Nota de licencia: AWR/ASH/ADDM forman parte del **Diagnostics & Tuning Pack** (de pago). En entornos sin licencia usar `Statspack`, `EXPLAIN PLAN`, `v$sql` y trazas 10046.

---

## 11. PL/SQL (bloques, procedimientos, jobs)

```sql
CREATE OR REPLACE PROCEDURE cerrar_pedido(p_id IN NUMBER) AS
BEGIN
  UPDATE pedidos SET estado='cerrado' WHERE id = p_id;
  COMMIT;
EXCEPTION
  WHEN OTHERS THEN
    ROLLBACK;
    RAISE;
END;
/

-- Bloque anónimo
BEGIN
  FOR r IN (SELECT id FROM pedidos WHERE estado='nuevo') LOOP
    cerrar_pedido(r.id);
  END LOOP;
END;
/
```

**Jobs programados** (`DBMS_SCHEDULER`):

```sql
BEGIN
  DBMS_SCHEDULER.CREATE_JOB(
    job_name        => 'job_stats',
    job_type        => 'PLSQL_BLOCK',
    job_action      => 'BEGIN DBMS_STATS.GATHER_SCHEMA_STATS(''APP''); END;',
    start_date      => SYSTIMESTAMP,
    repeat_interval => 'FREQ=DAILY; BYHOUR=2',
    enabled         => TRUE);
END;
/
SELECT job_name, state FROM dba_scheduler_jobs;
```

---

## 12. Parámetros de instancia

```sql
SHOW PARAMETER memory_target;            -- SQL*Plus
SELECT name, value FROM v$parameter WHERE name LIKE '%sga%';

ALTER SYSTEM SET sga_target = 4G SCOPE=BOTH;        -- memoria + spfile
ALTER SYSTEM SET open_cursors = 500 SCOPE=SPFILE;   -- requiere reinicio
```

| `SCOPE` | Efecto |
|---------|--------|
| `MEMORY` | Solo en caliente, se pierde al reiniciar. |
| `SPFILE` | Solo en el fichero, aplica al reiniciar. |
| `BOTH` | Ambos (cuando el parámetro es dinámico). |

---

## 13. Errores ORA frecuentes

| Código | Significado | Acción |
|--------|-------------|--------|
| `ORA-00942` | Tabla o vista no existe | Verificar nombre/esquema/privilegio. |
| `ORA-00001` | Violación de restricción única | Dato duplicado. |
| `ORA-01555` | Snapshot too old | Aumentar UNDO/`UNDO_RETENTION`. |
| `ORA-01017` | Usuario/contraseña inválidos | Credenciales o cuenta bloqueada. |
| `ORA-12541` | No listener | Listener parado / puerto / `tnsnames`. |
| `ORA-12154` | No se resuelve el descriptor TNS | Alias incorrecto en `tnsnames.ora`. |
| `ORA-01653` | No se puede extender tablespace | Añadir datafile / autoextend. |
| `ORA-00054` | Recurso ocupado (NOWAIT) | Otra sesión bloquea el objeto. |

```sql
SELECT * FROM v$diag_alert_ext WHERE rownum < 50;   -- alert log
-- o revisar $ORACLE_BASE/diag/.../trace/alert_<SID>.log
```

---

## Recursos

- [Oracle Database 23ai Documentation](https://docs.oracle.com/en/database/oracle/oracle-database/23/)
- [Oracle Base — artículos de DBA](https://oracle-base.com/)
- [SQL Language Reference](https://docs.oracle.com/en/database/oracle/oracle-database/23/sqlrf/)
- [RMAN User's Guide](https://docs.oracle.com/en/database/oracle/oracle-database/23/bradv/)
