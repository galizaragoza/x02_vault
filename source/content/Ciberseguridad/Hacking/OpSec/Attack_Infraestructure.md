# C2 Frameworks
Metasploit es rey en lo que a Linux se refiere, pero debido a su popularidad se cualquier antivirus lo flaguea la mínima. Hay otros frameworks C2 que son más efectivos en evadir los antivirus de hoy día.

- **Empire Framework**
	- Archivado en 2020, descontinuado por features de seguridad de Windows 10 como AMSI o PowerShell block logging
- **SharpSploit**
	- ".NET post-exploitation library written in C# that aims to highlight the attack surface of .NET and make the use of offensive .NET easier for red teamers."
- **Merlin**
	- Written in golang
- **Koadic**
	- https://github.com/offsecginger/koadic
- **SILENTTRINITY**
	- https://github.com/byt3bl33d3r/SILENTTRINITY

# Legacy Infraestructure

![[legacy-inf.png|575x207]]

La idea es redirigir el tráfico a través de un webserver al backend de C2, se registra un dominio aparentemente inocuo y se camufla, sin embargo, cierta ruta redirige a los backend desde los que operamos.

# Containers & Virtualization
Siendo tan volátil por su proximidad al objetivo, es lógico ahorrarse el setup de los servidores y optimizar el proceso mediante contenedores.

[[Docker 101]]

La idea es spawnear contenedores con lo necesario para llevar a cabo las operaciones, de manera que todo es tan sencillo como levantar el contenedor, esto da un entorno en el que se pueden mantener versiones de los contenedores y reemplazarlas a voluntad. Ya sea con un container previamente hecho por alguien o haciendo uno a mano con un Dockerfile.

Una vez los contenedores están listos para atacar, queda Dockerizar el servidor de Nginx que enruta las las llamadas los distintos backends de C2, para esto hay una heramienta que automatiza el setup de Nginx con certificados SSL.

[docker-nginx-certbot](https://github.com/JonasAlfredsson/docker-nginx-certbot/****)


# Recursos 
[Simple c2 inf. setup](https://youtu.be/WxtQnFDBPgI?list=PL65_wYSEg5HdSq1pSZxQmz7SoNE48vOgR), [repo del vídeo ](https://github.com/villaroot/simpleC2Redir)