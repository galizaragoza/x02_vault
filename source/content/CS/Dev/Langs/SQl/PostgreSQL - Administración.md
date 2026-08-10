# PostgreSQL — Administración

> Refresco rápido de operación y administración de PostgreSQL (12+). Conceptos transversales en [[SQL - Administración]]. Comparativa con Oracle en [[OracleDB - Administración]].

#Dev #SQL #PostgreSQL #DBA

---

## 1. Arquitectura

| Componente | Descripción |
|-----------|-------------|
| **Cluster** | Una instancia (`postmaster`) que gestiona varias *databases* sobre un `PGDATA`. |
| **postmaster** | Proceso supervisor; lanza un backend por conexión. |
| **WAL** (Write-Ahead Log) | Registro de cambios previo a datos; base de durabilidad, replicación y PITR. |
| **shared_buffers** | Cache de páginas en memoria compartida. |
| **MVCC** | Cada fila tiene versiones (`xmin`/`xmax`); lectores no bloquean escritores. |
| **Database → Schema → Objeto** | Jerarquía lógica. Schema por defecto: `public`. |
| **Tablespace** | Ubicación física alternativa para objetos. |

```sql
SELECT version();
SHOW data_directory;
SELECT current_database(), current_user;
```

---

## 2. psql — cliente y metacomandos

```bash
psql -h host -p 5432 -U usuario -d basedatos
psql "postgresql://usuario:clave@host:5432/db?sslmode=require"
sudo -u postgres psql                    # acceso local por peer
```

| Metacomando | Función |
|-------------|---------|
| `\l` | Listar bases de datos. |
| `\c db [user]` | Conectar a otra base. |
| `\dt` / `\dt+` | Tablas (con tamaño). |
| `\d tabla` | Estructura de un objeto. |
| `\dn` / `\df` / `\dv` | Esquemas / funciones / vistas. |
| `\du` | Roles y atributos. |
| `\dx` | Extensiones instaladas. |
| `\timing` | Mide tiempo de cada consulta. |
| `\x` | Salida vertical (expandida). |
| `\i fichero.sql` | Ejecuta un script. |
| `\copy` | Importa/exporta CSV desde el cliente. |
| `\e` / `\q` | Editar buffer / salir. |

```sql
\copy ventas TO 'ventas.csv' WITH (FORMAT csv, HEADER);
\copy ventas FROM 'ventas.csv' WITH (FORMAT csv, HEADER);
```

---

## 3. Gestión del servicio y de bases

```bash
# Servicio
sudo systemctl start|stop|restart|status postgresql
pg_ctl -D /var/lib/pgsql/data start|stop|restart|reload
pg_ctl reload                            # recarga config sin cortar conexiones

# Utilidades de SO
initdb -D /ruta/pgdata                   # crea un cluster
createdb -O dueño nombre_db
dropdb nombre_db
createuser --interactive
```

```sql
CREATE DATABASE tienda WITH OWNER app ENCODING 'UTF8' TEMPLATE template0;
ALTER DATABASE tienda SET timezone = 'UTC';
DROP DATABASE tienda;                     -- requiere sin conexiones activas
```

---

## 4. Roles y privilegios

En PostgreSQL **usuario y grupo son el mismo objeto**: un rol. `LOGIN` lo hace conectable.

```sql
CREATE ROLE app LOGIN PASSWORD 's3cr3t';
CREATE ROLE solo_lectura NOLOGIN;         -- rol de grupo
GRANT solo_lectura TO app;

GRANT CONNECT ON DATABASE tienda TO app;
GRANT USAGE ON SCHEMA public TO solo_lectura;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO solo_lectura;
-- Aplicar a objetos futuros:
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO solo_lectura;

ALTER ROLE app WITH PASSWORD 'nueva' VALID UNTIL '2027-01-01';
ALTER ROLE app CREATEDB;
REVOKE INSERT ON pedidos FROM app;
DROP ROLE app;                            -- reasignar/borrar objetos antes
```

| Atributo | Efecto |
|----------|--------|
| `SUPERUSER` | Salta todas las comprobaciones (restringir). |
| `CREATEDB` / `CREATEROLE` | Crear bases / roles. |
| `LOGIN` | Permite autenticarse. |
| `REPLICATION` | Para streaming/standby. |

---

## 5. Autenticación: pg_hba.conf

Controla quién conecta, desde dónde y cómo. Se evalúa **por orden, primera coincidencia gana**.

```
# TYPE  DATABASE  USER  ADDRESS         METHOD
local   all       all                   peer
host    all       all   127.0.0.1/32    scram-sha-256
host    tienda    app   10.0.0.0/24     scram-sha-256
hostssl all       all   0.0.0.0/0       scram-sha-256
```

| Método | Uso |
|--------|-----|
| `scram-sha-256` | Contraseña con hash robusto (preferido, PG 10+). |
| `md5` | Contraseña heredada (evitar). |
| `peer` | Usuario del SO = rol (solo conexiones locales). |
| `cert` | Certificado cliente TLS. |
| `trust` | Sin contraseña — **nunca en producción**. |

Tras editar: `pg_ctl reload` o `SELECT pg_reload_conf();`.

---

## 6. Configuración (postgresql.conf)

```sql
SHOW shared_buffers;
SELECT name, setting, unit FROM pg_settings WHERE name LIKE '%work_mem%';
ALTER SYSTEM SET shared_buffers = '2GB';  -- escribe en postgresql.auto.conf
SELECT pg_reload_conf();                  -- algunos requieren reinicio
SELECT name FROM pg_settings WHERE pending_restart;
```

| Parámetro | Función | Orientación |
|-----------|---------|-------------|
| `shared_buffers` | Cache principal. | ~25 % de la RAM. |
| `effective_cache_size` | Pista al planner sobre cache total. | ~50–75 % de la RAM. |
| `work_mem` | Memoria por operación de sort/hash. | Cuidado: se multiplica por conexión×operación. |
| `maintenance_work_mem` | Para `VACUUM`, `CREATE INDEX`. | Mayor que `work_mem`. |
| `max_connections` | Conexiones simultáneas. | Usar pooler si es alto. |
| `wal_level` | Detalle del WAL (`replica`/`logical`). | `replica` para standby/PITR. |

---

## 7. VACUUM, autovacuum y bloat

MVCC deja **tuplas muertas** tras `UPDATE`/`DELETE`. `VACUUM` las recupera.

```sql
VACUUM;                       -- recupera espacio reutilizable
VACUUM ANALYZE pedidos;       -- + actualiza estadísticas
VACUUM FULL pedidos;          -- reescribe la tabla, libera al SO (lock exclusivo)
ANALYZE pedidos;              -- solo estadísticas

-- Estado de autovacuum / tuplas muertas
SELECT relname, n_live_tup, n_dead_tup, last_autovacuum
FROM   pg_stat_user_tables ORDER BY n_dead_tup DESC;
```

- **autovacuum** lo hace automáticamente; no desactivarlo.
- **Wraparound**: el contador de transacciones (`xid`) es de 32 bits → vigilar `age(datfrozenxid)`; un *anti-wraparound vacuum* es obligatorio.

```sql
SELECT datname, age(datfrozenxid) FROM pg_database ORDER BY 2 DESC;
```

---

## 8. Índices

```sql
CREATE INDEX idx_cli ON pedidos(cliente_id);
CREATE INDEX CONCURRENTLY idx_cli ON pedidos(cliente_id);  -- sin bloquear escrituras
CREATE INDEX idx_act ON pedidos(creado) WHERE estado='activo';  -- parcial
CREATE INDEX idx_low ON usuarios((lower(email)));          -- sobre expresión
REINDEX INDEX idx_cli;
DROP INDEX CONCURRENTLY idx_cli;
```

| Tipo | Uso |
|------|-----|
| `btree` | Default; igualdad y rango. |
| `hash` | Solo igualdad. |
| `gin` | `jsonb`, arrays, full-text. |
| `gist` | Geometría, rangos, vecindad. |
| `brin` | Tablas enormes correlacionadas con el orden físico (poco espacio). |

```sql
-- Índices no usados (candidatos a eliminar)
SELECT relname, indexrelname, idx_scan
FROM   pg_stat_user_indexes WHERE idx_scan = 0;
```

---

## 9. Planes y rendimiento

```sql
EXPLAIN SELECT * FROM pedidos WHERE cliente_id = 5;
EXPLAIN (ANALYZE, BUFFERS) SELECT ...;    -- ejecuta y mide tiempos + I/O
EXPLAIN (ANALYZE, FORMAT JSON) SELECT ...;
```

Lectura del plan: nodos de abajo arriba; comparar `rows` estimadas vs. reales (`actual`); buscar *Seq Scan* costosos, *Sort* en disco, bucles con muchas iteraciones.

**pg_stat_statements** (consultas más caras):

```sql
CREATE EXTENSION pg_stat_statements;      -- + añadir a shared_preload_libraries
SELECT query, calls, total_exec_time, mean_exec_time
FROM   pg_stat_statements ORDER BY total_exec_time DESC LIMIT 10;
```

---

## 10. Sesiones, bloqueos y cancelación

```sql
-- Actividad actual
SELECT pid, usename, state, wait_event_type, query, query_start
FROM   pg_stat_activity WHERE state <> 'idle';

-- Cancelar consulta / terminar conexión
SELECT pg_cancel_backend(pid);            -- cancela la query
SELECT pg_terminate_backend(pid);         -- mata la conexión

-- Bloqueos en espera
SELECT * FROM pg_locks WHERE NOT granted;
```

`idle in transaction` prolongado bloquea el vacuum y retiene snapshots → vigilar `idle_in_transaction_session_timeout`.

---

## 11. Backup y recuperación

### Lógico

```bash
pg_dump -Fc -d tienda -f tienda.dump      # formato custom (restauración selectiva, paralela)
pg_dump -Fp -d tienda -f tienda.sql       # texto plano
pg_dumpall -g > globals.sql               # roles y tablespaces (todo el cluster)

pg_restore -d tienda -j 4 tienda.dump     # restauración paralela
pg_restore -d tienda -t pedidos tienda.dump   # solo una tabla
psql -d tienda -f tienda.sql
```

### Físico / PITR

```bash
pg_basebackup -D /backup/base -Fp -Xs -P  # copia base del cluster
```

PITR: copia base + archivado de WAL (`archive_command` / `archive_library`) + `recovery_target_time` en el arranque del standby. Permite restaurar a un instante exacto.

| Método | Granularidad | Nota |
|--------|--------------|------|
| `pg_dump` | Base / tabla | Portable entre versiones. |
| `pg_dumpall -g` | Roles globales | Complemento de `pg_dump`. |
| `pg_basebackup` + WAL | Cluster, PITR | Mismo major version/arquitectura. |

---

## 12. Replicación y alta disponibilidad

| Tipo | Descripción |
|------|-------------|
| **Streaming físico** | Standby aplica WAL en tiempo real; lectura en réplica (hot standby). |
| **Síncrona / asíncrona** | `synchronous_commit` decide si se espera al standby (durabilidad vs. latencia). |
| **Lógica** (`PUBLICATION`/`SUBSCRIPTION`) | Replica tablas concretas; permite versiones distintas y upgrades. |

```sql
-- Réplica lógica
CREATE PUBLICATION pub_ventas FOR TABLE pedidos, clientes;
-- En el suscriptor:
CREATE SUBSCRIPTION sub_ventas
  CONNECTION 'host=primario dbname=tienda user=repl'
  PUBLICATION pub_ventas;

-- Estado de réplicas
SELECT client_addr, state, sync_state, replay_lag FROM pg_stat_replication;
SELECT pg_is_in_recovery();               -- TRUE en el standby
```

Herramientas externas habituales: **Patroni** (HA con etcd/consul), **repmgr**, **PgBouncer**/**pgpool** (connection pooling), **pgBackRest**/**Barman** (backup PITR robusto).

---

## 13. Extensiones útiles

| Extensión | Uso |
|-----------|-----|
| `pg_stat_statements` | Estadísticas de consultas. |
| `pgcrypto` | Funciones de cifrado/hash. |
| `uuid-ossp` / `pgcrypto` | Generación de UUID. |
| `postgis` | Datos geoespaciales. |
| `pg_trgm` | Búsqueda por similitud / LIKE acelerado. |
| `pg_partman` | Gestión de particiones. |

```sql
CREATE EXTENSION IF NOT EXISTS pg_trgm;
SELECT * FROM pg_available_extensions;
```

---

## 14. Mantenimiento y diagnóstico

```sql
-- Tamaños
SELECT pg_size_pretty(pg_database_size('tienda'));
SELECT pg_size_pretty(pg_total_relation_size('pedidos'));   -- tabla + índices + toast

-- Cache hit ratio (objetivo > 0.99)
SELECT sum(heap_blks_hit)/nullif(sum(heap_blks_hit+heap_blks_read),0)
FROM   pg_statio_user_tables;

-- Conexiones por estado
SELECT state, count(*) FROM pg_stat_activity GROUP BY state;
```

Log de consultas lentas:

```sql
ALTER SYSTEM SET log_min_duration_statement = '500ms';
SELECT pg_reload_conf();
```

---

## Recursos

- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [PostgreSQL Wiki — Performance Optimization](https://wiki.postgresql.org/wiki/Performance_Optimization)
- [PGTune — generador de configuración](https://pgtune.leopard.in.ua/)
- [Use The Index, Luke! (PG)](https://use-the-index-luke.com/)
