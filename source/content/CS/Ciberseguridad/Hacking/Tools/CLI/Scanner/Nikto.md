**Nikto** es un escáner de vulnerabilidades de servidores web de código abierto. Realiza pruebas exhaustivas contra elementos de servidores web para detectar más de 6,700 archivos/programas potencialmente peligrosos, versiones desactualizadas y problemas de configuración específicos.

| **Parámetro**   | **Función**                                                    | **Ejemplo de Sintaxis**                     |
| --------------- | -------------------------------------------------------------- | ------------------------------------------- |
| `-h`, `-host`   | Especifica el host objetivo (IP, hostname o URL).              | `nikto -h http://target.com`                |
| `-p`, `-port`   | Especifica el puerto a escanear (por defecto 80).              | `nikto -h 192.168.1.1 -p 8080,443`          |
| `-ssl`          | Fuerza el uso de conexiones SSL/TLS.                           | `nikto -h target.com -ssl`                  |
| `-Cidr`         | Escanea un rango de IPs completo en formato CIDR.              | `nikto -h 192.168.1.0/24`                   |
| `-Tuning`       | Filtra el tipo de pruebas (ej. 1=XSS, 4=Inyección SQL).        | `nikto -h target.com -Tuning 4`             |
| `-o`, `-output` | Especifica el archivo de salida para los resultados.           | `nikto -h target.com -o reporte.html`       |
| `-Format`       | Define el formato del archivo de salida (csv, htm, txt, xml).  | `nikto -h target.com -o scan -Format xml`   |
| `-maxtime`      | Establece el tiempo máximo que puede durar el escaneo.         | `nikto -h target.com -maxtime 30m`          |
| `-evasion`      | Utiliza técnicas de evasión de IDS (1-8).                      | `nikto -h target.com -evasion 1`            |
| `-id`           | Especifica credenciales para autenticación básica (user:pass). | `nikto -h target.com -id admin:password123` |
| `-list-plugins` | Muestra todos los plugins que Nikto puede ejecutar.            | `nikto -list-plugins`                       |
| `-update`       | Actualiza la base de datos de firmas y plugins.                | `nikto -update`                             |
