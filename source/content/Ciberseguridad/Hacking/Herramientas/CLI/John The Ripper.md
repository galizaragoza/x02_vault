> John The Ripper es una herramienta de cracking empleada para ataques de diccionario y fuerza bruta

**Funciones principales**
- **Cracking de contraseñas**: Descifra hashes de contraseñas (MD5, SHA, etc.).
- **Modos de ataque**: Soporta fuerza bruta, diccionario y modo incremental.
- **Soporte multi-formato**: Compatible con múltiples algoritmos de hash (UNIX, Windows, etc.).
- **Personalización**: Permite reglas personalizadas para optimizar ataques.
```
john [opciones] <archivo_de_hashes>
```

|                 |                                                                |                                                        |
| --------------- | -------------------------------------------------------------- | ------------------------------------------------------ |
| `--wordlist`    | Define el archivo para usar en el ataque diccionario           | john --wordlist=rockyou.txt hash.txt                   |
| `--format`      | Define el formato de hash a crackear                           | john --format=md5crypt hash.txt                        |
| `--rules`       | Aplica reglas predefinidas al diccionario para crackear hashes | john --wordlist=rockyou.txt --rules hash.txt           |
| `--incremental` | Ejecuta un ataque incremental$^1$                              | john --incremental hash.txt                            |
| `--show`        | Muestra las contraseñas crackeadas                             | john --show hash.txt                                   |
| `--session`     | Guarda el progreso en una session para reaundarlo más adelante | john --session=ataque1 --wordlist=rockyou.txt hash.txt |
| `--restore`     | Reanuda una sesión previamente guardada                        | john --restore=ataque1                                 |
| `--fork`        | Utiliza múltiples procesos para acelerar un ataque             | john --fork=4 --wordlist=rockyou.txt hash.txt          |
|                 |                                                                |                                                        |
	1 --> Ejecuta un ataque de **fuerza bruta** generando todas las combinaciones posibles dentro de un conjunto de caracteres definido.
### ssh2john
> Extrae hash de clave privada SSH.
```bash
ssh2john id_rsa > ssh_hash.txt
```
### zip2john
> Extrae el hash de un archivo ZIP protegido
```
zip2john archivo.zip > zip_hash.txt
```
### rar2john
> Extrae el hash de un archivo RAR protegido
```
rar2john archivo.rar > rar_hash.txt
```
### pdf2john
> Extrae el hash de un archivo PDF protegido
```
pdf2john documento.pdf > pdf_hash.txt
```

## Ejemplo de uso
Suponiendo que, por ejemplo, hemos obtenido a clave SSH tunratando de ganar acceso a un sistema y queremos crackear dicha passphrase para ganar una shell privilegiada. Teniendo el archivo `id_rsa`, los pasos a seguir serían los siguientes:
1. **Extraer el hash** con ssh2john: `ssh2john id_rsa > ssh_hash.txt` 
2. Ejecutar un **ataque diccionario** con john: `john --wordlist=rockyou.txt --format=SSH ssh_hash.txt`
3. Ver los **resultados**: `john --show ssh_hash.txt` 
