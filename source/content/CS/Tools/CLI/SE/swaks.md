**Swaks** es la "navaja suiza" para el protocolo SMTP. Es una herramienta de línea de comandos extremadamente flexible y completa para probar servidores de correo, enviar emails de prueba o auditar vulnerabilidades en la configuración de correo.

|**Parámetro**|**Función**|**Ejemplo de sintaxis**|
|---|---|---|
|`--to`|Define el destinatario del correo.|`swaks --to user@example.com`|
|`--from`|Define el remitente (Envelope From).|`swaks --from admin@test.com`|
|`--server`|Especifica el servidor SMTP al que conectar.|`swaks --server smtp.gmail.com`|
|`--port`|Define el puerto de conexión (25, 465, 587).|`swaks --port 587`|
|`--auth`|Define el tipo de autenticación.|`swaks --auth LOGIN`|
|`--auth-user`|Nombre de usuario para la autenticación.|`swaks --auth-user "mi_usuario"`|
|`--auth-password`|Contraseña para la autenticación.|`swaks --auth-password "mi_pass"`|
|`--tls`|Fuerza el uso de TLS para la conexión.|`swaks --tls`|
|`--header`|Permite añadir cabeceras personalizadas.|`swaks --header "Subject: Test"`|
|`--body`|Define el cuerpo del mensaje.|`swaks --body "Hola mundo"`|
|`--attach`|Adjunta un archivo al correo.|`swaks --attach /ruta/file.txt`|
|`--quit-after`|Detiene el proceso tras una etapa específica.|`swaks --quit-after RCPT`|