
| Parámetro         | Función                 | Ejemplo de sintaxis                   |
| ----------------- | ----------------------- | ------------------------------------- |
| `-name "pat"`     | Buscar por nombre       | `find / -name "*.conf"`               |
| `-type f/d/l`     | Tipo archivo/dir/enlace | `find . -type f`                      |
| `-perm mode`      | Permisos exactos        | `find . -perm 777`                    |
| `-user name`      | Propietario             | `find /home -user alice`              |
| `-exec cmd {} \;` | Ejecutar comando        | `find . -name "*.tmp" -exec rm {} \;` |
