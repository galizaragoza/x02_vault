echo escribe sus argumentos en stdout seguidos de un newline. Existe como builtin en bash/zsh y como binario `/bin/echo` (GNU coreutils); el comportamiento de `-e` y `-n` puede diferir entre implementaciones y shells. Para portabilidad en scripts, `printf` es preferible cuando se necesitan secuencias de escape. Por defecto, varios argumentos se separan con un espacio.

```
echo [opciones] [cadena...]
```

---

## Opciones

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-n` | No añade newline al final de la salida. | `echo -n "sin salto"` |
| `-e` | Habilita la interpretación de secuencias de escape con `\`. | `echo -e "línea1\nlínea2"` |
| `-E` | Deshabilita la interpretación de secuencias de escape (comportamiento por defecto). | `echo -E "literal \n sin escape"` |

---

## Secuencias de escape (con `-e`)

| Secuencia | Significado |
|-----------|-------------|
| `\\` | Barra invertida literal. |
| `\n` | Nueva línea (newline). |
| `\t` | Tabulación horizontal. |
| `\r` | Retorno de carro. |
| `\a` | Alerta / campana (BEL). |
| `\b` | Retroceso (backspace). |
| `\f` | Avance de formulario (form feed). |
| `\v` | Tabulación vertical. |
| `\0NNN` | Byte con valor octal NNN (1-3 dígitos). |
| `\xHH` | Byte con valor hexadecimal HH. |
| `\uHHHH` | Carácter Unicode de 16 bits (UTF-8). |
| `\UHHHHHHHH` | Carácter Unicode de 32 bits (UTF-8). |
| `\c` | Suprime el resto de la salida (no imprime nada más, ni newline). |

---

## Casos de uso comunes

```bash
# Texto básico
echo "Hola mundo"

# Sin newline final (útil en scripts para prompt inline)
echo -n "Introduce tu nombre: "

# Múltiples líneas en una sola llamada
echo -e "línea 1\nlínea 2\nlínea 3"

# Caracteres de color ANSI (requiere -e)
echo -e "\e[31mRojo\e[0m \e[32mVerde\e[0m"

# Escribir en fichero
echo "contenido" > fichero.txt
echo "más contenido" >> fichero.txt

# Redirigir stderr a un fichero de log (no requiere echo, es del shell)
echo "error message" >&2

# Bytes hexadecimales (útil para payloads)
echo -e "\x41\x42\x43"   # ABC

# Alternativa portable con printf
printf "línea1\nlínea2\n"
printf "%s\n" "valor"
```
