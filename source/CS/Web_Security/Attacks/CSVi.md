### ¿Qué es el CSVi?
El CSVi o CSV Injection, también conocida como Formula Injection, sucede cuando un sitio web embebe inputs no sanitizados en archivos CSV $¹$. Cuando un programa de hojas de cálculo como Excel o Calc se utiliza para abrir un archivo CSV, cualquier celda que comienze por `=` será interpretada por el software como una fórmula, aprovechando esta funcionalidad se pueden confeccionar fórmulas maliciosas para una serie de propósitos.
1. Comprometer la máquina abriendo el archivo a través de vulnerabilidades en el software que interpreta estos archivos (Excel, Calc...)
2. Comprometer la máquina aprovechándose de la tendencia a ignorar advertencias que provienen de archivos descargados de una web confiada(p.ej: la web de la empresa en la que trabajas)
3. Exfiltrar el contenido del CSV que se está inyectado u otros alojados en el mismo servidor.
Las fórmulas pueden empezar con estos caracteres:
```csv
=
+
-
@
```
La forma de protegerse frente a esta vulnerabilidad consiste en securizar el documento, de manera que las celdas no comiencen por estos caracteres:
```
=
+
-
@
0x09
0x0D
0x0A
## También, en Google Sheets se permiten algunas funciones que hacen fetch a URLs remotas
IMPORTXML(url, xpath_query, locale)
IMPORTRANGE(url_hoja_cálculo; cadena_intervalo)
IMPORTHTML(url, consulta, índice)
IMPORTFEED(url; [consulta]; [encabezados]; [número_elementos])
IMPORTDATA(url)
```
Sin embargo, protegerse de esto es a menudo insuficiente, ya que también hay que cuidarse de `,`, `;`, `'` y `"`, ya que pueden ser utilizados por el atacante para crear una nueva celda.
 
>1--> Un archivo CSV (Comma Separated Value) es un archivo de texto en el cuál los valores están separados por comas, creando una tabla de filas y columnas. Cada columna es definidia por un punto y coma (;).

### Recursos
### Artículos
[Reporte de HackerOne con una demostración real de la explotación](https://hackerone.com/reports/1748961)
[OWASP sobre CSV](https://owasp.org/www-community/attacks/CSV_Injection)
[PayLoadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/CSV%20Injection#methodology)
[Ejemplos de vectores de ataque](https://georgemauer.net/2017/10/07/csv-injection.html)