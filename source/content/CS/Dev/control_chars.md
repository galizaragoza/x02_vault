#reference

**Caracteres de control** = bytes no imprimibles que controlan el formato o el flujo, no representan texto visible. Aparecen como escapes (`\n`, `\t`) en cadenas y son relevantes en parsing, inyección y manejo de ficheros binarios.

# Escapes comunes

| Escape | Nombre | Byte (hex) |
|--------|--------|-----------|
| `\n` | Newline (LF) | `0x0A` |
| `\r` | Carriage return (CR) | `0x0D` |
| `\t` | Tab | `0x09` |
| `\b` | Backspace | `0x08` |
| `\f` | Form feed | `0x0C` |
| `\v` | Vertical tab | `0x0B` |
| `\0` | Null | `0x00` |
| `\xFF` | Byte arbitrario (hex) | — |
| `\a` | Bell | `0x07` |
| `\e` | Escape (ANSI) | `0x1B` |

# Saltos de línea por SO

| SO | Fin de línea |
|----|--------------|
| Unix/Linux/macOS | `\n` (LF) |
| Windows | `\r\n` (CRLF) |
| macOS clásico | `\r` (CR) |

# Relevancia en seguridad

| Carácter | Vector |
|----------|--------|
| `\r\n` (CRLF) | **CRLF injection** → HTTP response splitting, inyección de cabeceras/logs. |
| `\0` (null byte) | **Null byte injection** → truncar rutas/strings en C/PHP antiguos (`file.php%00.jpg`). |
| `\x1B` (ESC) | Inyección de secuencias ANSI en terminales/logs. |
| Caracteres invisibles/unicode | Homoglyph / spoofing, bypass de filtros. |

```bash
# Ver caracteres de control en un fichero
cat -A fichero        # muestra $ (LF), ^M (CR), ^I (tab)
xxd fichero | head    # bytes en hex
tr -d '\r' < win.txt > unix.txt   # convertir CRLF -> LF
```

# Recursos
### [Tabla ASCII](https://www.asciitable.com/) · [Wikipedia — Control character](https://en.wikipedia.org/wiki/Control_character)
