tee lee de stdin y escribe simultáneamente a stdout y a uno o más ficheros. Su nombre viene del divisor en T de fontanería: bifurca el flujo de datos. Es esencial en pipelines para guardar resultados intermedios sin interrumpir la cadena, o para escribir en múltiples destinos a la vez. Puede usarse con `sudo tee` para escribir en ficheros que requieren privilegios de root.

```
tee [opciones] [fichero...]
```

---

## Opciones

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-a` / `--append` | Añade al final de los ficheros en lugar de sobreescribirlos. | `ls -la \| tee -a listado.log` |
| `-i` / `--ignore-interrupts` | Ignora señales de interrupción (SIGINT). Útil en scripts de larga duración. | `comando \| tee -i salida.txt` |
| `-p` | Detecta errores en la escritura a stdout sin propagarlos (modo pipe-safe). | `comando \| tee -p log.txt` |
| `--output-error[=MODO]` | Controla el comportamiento ante errores de escritura: `warn` (avisa), `warn-nopipe` (avisa salvo broken pipe), `exit` (sale), `exit-nopipe` (sale salvo broken pipe). | `comando \| tee --output-error=warn log.txt` |
| `--help` | Muestra ayuda. | `tee --help` |
| `--version` | Muestra versión. | `tee --version` |

---

## Casos de uso comunes

```bash
# Guardar salida y seguir viendo en terminal
ls -la | tee listado.txt

# Añadir a fichero existente sin sobreescribir
echo "nueva entrada" | tee -a log.txt

# Escribir en múltiples ficheros a la vez
comando | tee fichero1.txt fichero2.txt

# Escribir en fichero con privilegios de root (evita sudo >)
echo "dato" | sudo tee /etc/configuracion.conf

# Añadir con sudo sin sobreescribir
echo "linea extra" | sudo tee -a /etc/hosts

# Guardar salida intermedia en pipeline
cat datos.txt | tee datos_raw.txt | grep "ERROR" | tee errores.txt | wc -l

# Ignorar Ctrl+C durante proceso largo
long_running_cmd | tee -i resultado.log

# Ver y guardar salida de compilación
make 2>&1 | tee build.log
```
