Muestra el nombre de usuario actual y detalles de seguridad en Windows.

```
whoami [opciones]
```

| **Parámetro (Flag)** | **Función**                                                           | **Ejemplo de Sintaxis**      |
| ---------------- | ----------------------------------------------------------------- | ------------------------ |
| `/user`          | Muestra nombre de usuario y SID.                                  | `whoami /user`           |
| `/groups`        | Muestra grupos del usuario.                                       | `whoami /groups`         |
| `/priv`          | Muestra privilegios del usuario.                                  | `whoami /priv`           |
| `/all`           | Muestra toda la información (usuario, grupos, privilegios, etc.). | `whoami /all`            |
| `/fo FORMAT`     | Especifica formato de salida (TABLE, LIST, CSV).                  | `whoami /groups /fo CSV` |
| `/upn`           | Muestra nombre principal de usuario (UPN).                        | `whoami /upn`            |
| `/fqdn`          | Muestra nombre de dominio completo (FQDN).                        | `whoami /fqdn`           |
| `/logonid`       | Muestra ID de inicio de sesión.                                   | `whoami /logonid`        |
| `/?`             | Muestra ayuda.                                                    | `whoami /?`              |