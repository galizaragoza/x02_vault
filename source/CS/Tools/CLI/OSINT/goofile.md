**Goofile** es una herramienta de OSINT (Open Source Intelligence) que utiliza los dorks de Google para encontrar archivos específicos (PDF, DOC, XLS, etc.) alojados en un dominio determinado.

| **Parámetro** | **Función**                           | **Ejemplo de Sintaxis**      |
| ------------- | ------------------------------------- | ---------------------------- |
| `-d`          | Dominio donde realizar la búsqueda.   | `goofile -d nasa.gov`        |
| `-f`          | Tipo de archivo (extensión) a buscar. | `goofile -d nasa.gov -f pdf` |
| `-h`          | Muestra la ayuda de la herramienta.   | `goofile -h`                 |