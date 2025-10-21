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
exiftool [opciones] <archivo(s)>
```

|                       |                                                            |                                                          |
| --------------------- | ---------------------------------------------------------- | -------------------------------------------------------- |
| `-a`                  | Muestra todos los metadatos                                | exiftool -a image.jpg                                    |
| `-g`                  | Agrupa los metadatos por categoría (EXIF, IPTC, XMP, etc.) | exiftool -g image.jpg                                    |
| `-u`                  | Incluye etiquetas desconocidas o no estándar               | exiftool -u image.jpg                                    |
| `-r`                  | Procesa un directorio completo de forma recursiva          | exiftool -r /path/to/folder                              |
| `-ext`                | Filtra archivos por extensión                              | exiftool -ext jpg /path/to/folder                        |
| `-overwrite_original` | Sobreescribe el archivo original con nuevos metadatos      | exiftool -overwrite_original -Comment="Hacked" image.jpg |
| `-all=`               | Elimina todos los metadatos editablea                      | exiftool -all= image.jpg                                 |
| `-json`               | Genera salida en formato JSON                              | exiftool -json image.jpg                                 |
| `-csv`                | Genera salida en formato CSV                               | exiftool -csv /path/to/folder > metadata.csv             |
| `-tagsFromFile`       | Copia todos los metadatos de un archivo a otro             | exiftool -tagsFromFile source.jpg -all:all target.jpg    |
|                       |                                                            |                                                          |
