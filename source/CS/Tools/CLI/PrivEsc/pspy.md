> pspy es una herramienta que permite monitorear procesos en sistemas Linux sin necesidad de privilegios root, con ella podemos observar procesos, comandos ejecutados y eventos del sistema.

**Funciones principales**
- **Monitoreo de procesos**: Lista procesos en ejecución, sus argumentos y relaciones entre ellos.
- **Captura de comandos**: Registra comandos ejecutados, incluyendo argumentos completos.
- **Detección de tareas programadas**: Identifica tareas cron y otros eventos automatizados.
- **Análisis sin privilegios**: Funciona sin acceso administrativo, ideal para entornos limitados.
- **Detección de sockets**: Monitorea conexiones de red creadas por procesos.
**Usos comunes**
- **Pentesting**: Identifica procesos o tareas cron que puedan ser explotados para escalar privilegios.
- **Hacking ético**: Analiza sistemas en CTF o auditorías autorizadas para encontrar configuraciones débiles.
- **Análisis forense**: Detecta actividades maliciosas o inusuales en sistemas comprometidos.
- **Monitoreo de sistemas**: Observa comportamientos de procesos para administradores en entornos seguros.
```
./pspy [opciones]
```

|      |                                                                                 |                |
| ---- | ------------------------------------------------------------------------------- | -------------- |
| `-p` | Muestra solo los procesos en ejecución (ignora eventos del sistema de archivos) | ./pspy -p      |
| `-f` | Muestra solo eventos del sistema de archivos                                    | ./pspy -f      |
| `-r` | Incluye eventos de red (sockets)                                                | ./pspy -r      |
| `-d` | Muestra detalles completos de comandos y argumentos                             | ./pspy -d      |
| `-i` | Define el delay entre procesos                                                  | ./pspy -i 1000 |
|      |                                                                                 |                |
