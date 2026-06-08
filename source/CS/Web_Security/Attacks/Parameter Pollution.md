#reference #Web_Security

**HTTP Parameter Pollution (HPP)** = enviar el **mismo parámetro varias veces** en una petición. Cada tecnología (servidor, framework, WAF) decide de forma distinta cuál valor toma → discrepancias que permiten bypass de filtros, de lógica o de validación.

# Cómo lo interpreta cada stack

| Tecnología | `?x=a&x=b` toma |
|------------|----------------|
| PHP / Apache | el **último** (`b`) |
| ASP / IIS | **concatena** (`a,b`) |
| ASP.NET | **concatena** (`a,b`) |
| JSP / Tomcat | el **primero** (`a`) |
| Node/Express | **array** (`['a','b']`) |
| Python (Flask) | el **primero** |

> El bug surge cuando dos capas (WAF y app, o frontend y backend) eligen valores **distintos** del mismo parámetro.

# Tipos

- **Server-side (HPP):** el backend procesa una copia del parámetro distinta de la que validó el WAF/filtro → bypass de WAF, de control de acceso, de business logic.
- **Client-side (HPP):** inyección en URLs/enlaces generados que añaden parámetros → manipular links, CSRF, abrir vectores XSS.

# Ejemplos de abuso

```
# Bypass de filtro: el WAF ve el primero, la app el último
/transfer?amount=1&amount=10000

# Saltarse control de acceso por discrepancia de parsing
/api?role=user&role=admin

# Client-side: contaminar un enlace generado
/share?url=ok&url=evil
```

# Detección

Duplicar cada parámetro con valores distintos y observar cambios en la respuesta. Probar combinaciones front/back. Útil en endpoints con WAF delante.

# Mitigación

Normalizar parámetros (rechazar duplicados), validar tipo/cantidad, mismo motor de parsing en todas las capas, allow-list de parámetros esperados.

# Recursos
### [PortSwigger / HackTricks — HTTP Parameter Pollution](https://book.hacktricks.xyz/pentesting-web/parameter-pollution)
### [OWASP — Testing for HPP](https://owasp.org/www-project-web-security-testing-guide/)
### [PayloadsAllTheThings — HTTP Parameter Pollution](https://github.com/swisskyrepo/PayloadsAllTheThings)
