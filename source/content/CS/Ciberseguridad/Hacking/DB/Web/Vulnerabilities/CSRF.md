Cross Site Request Forgery es un ataque que fuerza a un usuario a ejecutar una acción no deseada en una aplicación web en la que estén autenticados, CSRF ataca específicamente a solicitudes de cambio de estado (crear, editar, borrar).
Por ejemplo, podríamos toparnos con un enlace `clickme.com` mientras navegamos por una aplicación vulnerable. Al clicar en este enlace, lo que este hace por detrás es mandar una solicitud al endpoint no protegido de la aplicación en cuestión, por ejemplo, si el enlace estuviese mandando un POST a /delete_my_account, el ataque consistiría en tomar el token de sesión vigente en tu navegador para hacer la petición a este endpoint en tu nombre.
![[CSRF-CheatSheet.png]]

# CSRF Bypassses
### CSRF Tokens
### SameSite Cookies
### Referer-based validation
## Recursos
[XSRF Probe - Detector de vulnerabilidades XRSF](https://github.com/0xInfection/XSRFProbe)
[PayloadsAllTheThings Art.](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Cross-Site%20Request%20Forgery#methodology)
[# CSRF Deep Dive | Bug Bounty Hunters Guide to Cross-Site Request Forgery](https://youtu.be/Qjje64LOSeE?list=PL65_wYSEg5HdSq1pSZxQmz7SoNE48vOgR)
[CSRF CS](https://jasper-join-7e5.notion.site/CSRF-Cheat-Sheet-a3a50eb632c94583a8760b4db4d5face)