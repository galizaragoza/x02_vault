```sh
User_Alias ADMINS = user1, user2 ## Define alias ADMINS

ADMINS ALL = NOPASSWD: ALL ## Todos (primer ALL) los admins pueden ejecutar todos (segundo ALL) los comandos sin proporcionar contraseña

root ALL=(ALL) ALL ## root puede ejecutar cualquier comando sin contraseña el (ALL) significa que también puede suplantar a cualquier usuario (sudo -u user1 cmd)

```
