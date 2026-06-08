**Searchsploit** es la interfaz de línea de comandos para **Exploit-DB**. Permite buscar exploits almacenados localmente (normalmente en `/usr/share/exploitdb`) sin necesidad de conexión a internet.

| **Parámetro** | **Función**                                            | **Ejemplo de sintaxis**               |
| ------------- | ------------------------------------------------------ | ------------------------------------- |
| `-m`          | Copia el exploit seleccionado al directorio actual.    | `searchsploit -m 12345`               |
| `-p`          | Muestra la ruta completa del exploit.                  | `searchsploit -p 12345`               |
| `-x`          | Examina (lee) el contenido del exploit en la terminal. | `searchsploit -x 12345`               |
| `-t`          | Realiza una búsqueda limitada estrictamente al título. | `searchsploit -t apache`              |
| `-w`          | Muestra la URL de Exploit-DB en lugar del path local.  | `searchsploit -w windows 10`          |
| `-u`          | Actualiza la base de datos de exploits.                | `searchsploit -u`                     |
| `--json`      | Muestra los resultados en formato JSON.                | `searchsploit --json wordpress`       |
| `--exclude`   | Excluye términos específicos de la búsqueda.           | `searchsploit apache --exclude "2.4"` |