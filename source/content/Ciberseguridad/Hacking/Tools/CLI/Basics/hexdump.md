Mostrar el contenido de archivos en formato hexadecimal, octal, decimal o ASCII. Ideal para inspeccionar binarios, depurar datos crudos y analizar malware/exploits en hacking.
> Exiftool es una herramienta que permite acceder a los metadatos asociados a un determinado archivo (img, pdf, video...), a menudo relevando información importante como usernames, localizaciones, dispositivos, software... 
### **Funciones principales**
1. **Extracción de metadatos**: Lee metadatos de archivos (EXIF, IPTC, XMP, etc.) para identificar información sensible.
2. **Edición de metadatos**: Modifica o elimina metadatos para pruebas de ingeniería social o limpieza de archivos.
3. **Soporte multi-formato**: Compatible con imágenes (JPEG, PNG), PDFs, MP4, y más.
4. **Análisis forense**: Ayuda a descubrir datos ocultos en archivos para investigaciones de seguridad.
5. **Automatización**: Permite scripts para procesar múltiples archivos en auditorías.
### **Usos comunes en pentesting y ciberseguridad**
- **Reconocimiento**: Extrae datos como nombres de usuario, fechas, o coordenadas GPS de imágenes en sitios web o redes sociales.
- **Ingeniería social**: Identifica información (ej. software de edición, nombres de dispositivos) para personalizar ataques.
- **Análisis forense**: Examina archivos para encontrar pistas en investigaciones de incidentes.
- **Limpieza de metadatos**: Elimina datos sensibles antes de compartir archivos en auditorías.
- **Bug bounties**: Busca información expuesta en archivos públicos de una organización.
```
hexdump [opciones] archivo
```

| **Parámetro** | **Función**                                         | **Ejemplo de uso**                   |
| --------- | ----------------------------------------------- | -------------------------------- |
| -C        | Formato canónico (hex + ASCII, 16 bytes/línea)  | hexdump -C malware.bin           |
| -v        | Muestra todas las líneas (no colapsa repetidas) | hexdump -v -C shellcode.bin      |
| -n length | Limita salida a N bytes                         | hexdump -n 64 -C payload.bin     |
| -s offset | Salta los primeros N bytes (skip)               | hexdump -s 0x100 -C firmware.bin |
| -e format | Formato personalizado                           | hexdump -e '4/4 "%08X "' file    |
| -x        | Solo hexadecimal (2 bytes por grupo)            | hexdump -x shellcode             |
| -d        | Solo decimal                                    | hexdump -d datos.bin             |
| -o        | Solo octal                                      | hexdump -o archivo               |
| -b        | Dump en bytes (octal, 1 byte)                   | hexdump -b raw_socket            |
| -c        | Solo caracteres ASCII                           | hexdump -c texto_oculto.bin      |