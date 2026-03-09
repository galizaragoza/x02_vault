| **Operación / Parámetro** | **Función**                                                                          | **Ejemplo de Sintaxis**                    |
| ------------------------- | ------------------------------------------------------------------------------------ | ------------------------------------------ |
| `-S`                      | **Sincronizar:** Instala paquetes o actualiza la base de datos.                      | `sudo pacman -S nombre_paquete`            |
| `-Syu`                    | **Actualización completa:** Sincroniza repositorios y actualiza todo el sistema.     | `sudo pacman -Syu`                         |
| `-Ss`                     | **Buscar:** Busca coincidencias en los repositorios remotos.                         | `pacman -Ss palabra_clave`                 |
| `-Si`                     | **Información:** Muestra detalles de un paquete en los repositorios.                 | `pacman -Si nombre_paquete`                |
| `-R`                      | **Eliminar:** Quita un paquete del sistema.                                          | `sudo pacman -R nombre_paquete`            |
| `-Rs`                     | **Eliminar con dependencias:** Quita el paquete y sus dependencias no usadas.        | `sudo pacman -Rs nombre_paquete`           |
| `-Rns`                    | **Limpieza total:** Elimina paquete, dependencias y archivos de configuración.       | `sudo pacman -Rns nombre_paquete`          |
| `-U`                      | **Instalar local:** Instala un paquete desde un archivo local o URL.                 | `sudo pacman -U /ruta/paquete.pkg.tar.zst` |
| `-Q`                      | **Consultar:** Lista los paquetes instalados localmente.                             | `pacman -Q`                                |
| `-Qi`                     | **Información local:** Muestra info detallada de un paquete ya instalado.            | `pacman -Qi nombre_paquete`                |
| `-Ql`                     | **Listar archivos:** Muestra todos los archivos instalados por un paquete.           | `pacman -Ql nombre_paquete`                |
| `-Qk`                     | **Verificar:** Comprueba si los archivos del paquete existen en el sistema.          | `pacman -Qk nombre_paquete`                |
| `-F`                      | **Archivos:** Busca a qué paquete pertenece un archivo específico.                   | `pacman -F /usr/bin/ls`                    |
| `-Sc`                     | **Limpiar caché:** Elimina paquetes antiguos de la caché (`/var/cache/pacman/pkg/`). | `sudo pacman -Sc`                          |

| **Utilidad**   | **Función**                                                                      | **Ejemplo de Uso**       |
| -------------- | -------------------------------------------------------------------------------- | ------------------------ |
| `makepkg`      | Herramienta para compilar paquetes desde scripts `PKGBUILD`.                     | `makepkg -si`            |
| `pacdiff`      | Gestiona archivos de configuración nuevos (`.pacnew`) generados tras actualizar. | `sudo pacdiff`           |
| `pactree`      | Muestra el árbol de dependencias de un paquete específico.                       | `pactree nombre_paquete` |
| `checkupdates` | Comprueba actualizaciones pendientes sin descargar ni tocar la DB real.          | `checkupdates`           |