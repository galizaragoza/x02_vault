Cliente Telnet (TCP puro). Conecta a cualquier puerto TCP y envía/recibe texto plano.

```
telnet IP puerto
```

| Parámetro/Comando      | Función                                | Ejemplo de sintaxis     |
| ---------------------- | -------------------------------------- | ----------------------- |
| `telnet` (ip) (puerto) | Conexión directa                       | `telnet 10.10.10.10 25` |
| ^] (Ctrl + ])          | Entrar modo comando telnet             | (dentro de sesión)      |
| `quit`                 | Salir del modo comando y cerrar        | `quit`                  |
| `open` (ip) (puerto)   | Conectar desde modo comando            | `open 192.168.1.1 8443` |
| `set localecho`        | Ver lo que escribes (útil en exploits) | `set localecho`         |
| `unset localecho`      | Ocultar input (contraseñas)            | `unset localecho`       |

