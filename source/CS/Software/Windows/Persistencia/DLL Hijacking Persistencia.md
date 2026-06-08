#reference #Ciberseguridad

**DLL Hijacking como persistencia** (MITRE **T1574.001**) = plantar una DLL maliciosa que una aplicación legítima (o servicio que arranca con el sistema) cargará automáticamente cada vez que se ejecute → ejecución recurrente sin proceso extra sospechoso.

> Misma técnica que el [[DLL Hijacking PrivEsc|hijacking de privesc]], pero el objetivo aquí es **mantener acceso**, no escalar. Se elige una app que arranque al inicio o se use a menudo.

# Vías de persistencia

| Vector | Idea |
|--------|------|
| **Search-order hijacking** | Plantar DLL en una ruta que la app busca antes que la real. |
| **Phantom DLL** | DLL que la app pide pero no existe → la creamos nosotros. |
| **DLL side-loading** | App firmada carga una DLL maliciosa junto a su binario (abusa de la confianza en la firma). |
| **DLL proxying** | Reenviar funciones a la DLL legítima para no romper la app y pasar desapercibido. |

# Por qué es sigiloso

- Se ejecuta dentro de un **proceso legítimo y firmado** → evade detección por reputación de proceso.
- No crea servicios/tareas obvias.
- Sobrevive a reinicios si la app objetivo arranca al inicio.

# Detección / mitigación

- Monitorizar cargas de DLLs no firmadas / desde rutas inusuales (Sysmon Event ID 7).
- Comparar DLLs cargadas con líneas base; verificar firmas.
- `Procmon` para `NAME NOT FOUND` en .dll.
- Quitar permisos de escritura en directorios de aplicaciones; rutas absolutas.

# Recursos
### [[DLL Hijacking PrivEsc]] · [[Startup]]
### [MITRE ATT&CK — T1574.001](https://attack.mitre.org/techniques/T1574/001/)
