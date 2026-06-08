#reference #Web_Security

**Race condition** = fallo lógico en el que dos o más peticiones que operan sobre el **mismo recurso compartido casi simultáneamente** rompen una invariante porque la comprobación y el uso no son atómicos (**TOCTOU**: Time-Of-Check to Time-Of-Use).

# Cuándo aparece

Entre el momento en que la app **comprueba** un estado y el momento en que **actúa** sobre él, otra petición cambia ese estado. La ventana suele ser de milisegundos.

| Patrón vulnerable | Ejemplo de abuso |
|-------------------|------------------|
| Límite de uso de un recurso | Canjear un cupón/gift card N veces |
| Comprobar saldo → restar | Gastar el mismo saldo dos veces (overdraft) |
| Comprobar stock → reservar | Comprar más unidades de las disponibles |
| Validar → ejecutar | Bypass de límites de intentos / MFA |
| Crear si no existe | Registrar el mismo usuario/email dos veces |

# Explotación

Clave: enviar muchas peticiones **simultáneas** para que caigan dentro de la misma ventana.

```
single-packet attack  (HTTP/2): mandar N peticiones en un solo paquete TCP
                                 → elimina el jitter de red, máximo solapamiento
last-byte sync        (HTTP/1.1): enviar todo menos el último byte de cada
                                  petición, luego soltar los últimos a la vez
```

| Herramienta | Uso |
|-------------|-----|
| **Burp Repeater** (group → *Send group in parallel*) | Single-packet attack nativo. |
| **Turbo Intruder** | Script Python, control fino de concurrencia. |
| `ffuf` / scripts con hilos | Aproximación rápida (menos preciso). |

```python
# Turbo Intruder — disparo paralelo
engine = RequestEngine(endpoint=target, concurrentConnections=30,
                       engine=Engine.BURP2)
for i in range(30):
    engine.queue(request)
engine.openGate()   # libera todas a la vez
```

# Tipos

- **Limit-overrun**: superar un límite contado (saldo, cupones, votos).
- **Multi-endpoint**: combinar dos endpoints que tocan el mismo objeto (ej. añadir al carrito + aplicar descuento).
- **Single-endpoint**: colisión de dos peticiones al mismo endpoint.

# Mitigación

Operaciones atómicas en BD (`SELECT ... FOR UPDATE`, transacciones serializables), locks/mutex, restricciones únicas, tokens de un solo uso, comprobaciones idempotentes.

# Recursos
### [PortSwigger — Race conditions](https://portswigger.net/web-security/race-conditions)
### [Race Conditions — Bug Hunters Guide](https://youtu.be/n1VcjH8P7gQ?list=PL65_wYSEg5HdSq1pSZxQmz7SoNE48vOgR)
### [PayloadsAllTheThings — Race Condition](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Race%20Condition)
