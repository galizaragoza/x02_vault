systemctl es una herramienta de línea de comandos para inspeccionar y controlar el estado del gestor de sistema y servicios systemd en Linux. Permite gestionar unidades como servicios, sockets, dispositivos, montajes, etc., incluyendo operaciones locales y remotas.

# list-units
Lista unidades en memoria.

| **Flag**    | **Descripción**                     | **Ejemplo**                           |
| ----------- | ----------------------------------- | ------------------------------------- |
| `--all`     | Muestra también unidades inactivas. | `systemctl list-units --all`          |
| `--type=`   | Filtra por tipo de unidad.          | `systemctl list-units --type=service` |
| `--state=`  | Filtra por estado de unidad.        | `systemctl list-units --state=failed` |
| `--quiet`   | Suprime salida.                     | `systemctl list-units --quiet`        |
| `--output=` | Formato de salida corto.            | `systemctl list-units --output=short` |
# list-socket
Lista sockets en memoria.

| **Flag**           | **Descripción**                | **Ejemplo**                                    |
| -------------- | -------------------------- | ------------------------------------------ |
| `--all`        | Muestra también inactivos. | `systemctl list-sockets --all`             |
| `--state=`     | Filtra por estado.         | `systemctl list-sockets --state=listening` |
| `--show-types` | Muestra tipos de unidades. | `systemctl list-sockets --show-types`      |
# list-timers
Lista timers en memoria.

| **Flag**       | **Descripción**                | **Ejemplo**                                 |
| ---------- | -------------------------- | --------------------------------------- |
| `--all`    | Muestra también inactivos. | `systemctl list-timers --all`           |
| `--state=` | Filtra por estado.         | `systemctl list-timers --state=waiting` |
# status
Muestra estado de unidades.

| **Flag**       | **Descripción**                  | **Ejemplo**                           |
| ---------- | ---------------------------- | --------------------------------- |
| `--all`    | Muestra todos.               | `systemctl status --all`          |
| `--lines=` | Número de líneas de journal. | `systemctl status --lines=30`     |
| `--full`   | Sin elipsis en salida.       | `systemctl status --full`         |
| `--type=`  | Filtra por tipo.             | `systemctl status --type=service` |
| `--state=` | Filtra por estado.           | `systemctl status --state=failed` |
| `--failed` | Solo fallidas.               | `systemctl status --failed`       |
| `--quiet`  | Suprime salida.              | `systemctl status --quiet unit`   |
# show
Muestra propiedades de unidades.

| **Flag**          | **Descripción**                    | **Ejemplo**                             |
| ------------- | ------------------------------ | ----------------------------------- |
| `--all`       | Muestra todas las propiedades. | `systemctl show --all`              |
| `--property=` | Propiedades específicas.       | `systemctl show --property=MainPID` |
| `--value`     | Solo valores.                  | `systemctl show --value`            |
| `--type=`     | Filtra por tipo.               | `systemctl show --type=service`     |
# start

| **Flag**          | **Descripción**                       | **Ejemplo**                                   |
| ------------- | --------------------------------- | ----------------------------------------- |
| `--all`       | Opera en inactivas referenciadas. | `systemctl start --all unit`              |
| `--job-mode=` | Modo de jobs.                     | `systemctl start --job-mode=replace unit` |
| `--no-block`  | Sin esperar.                      | `systemctl start --no-block unit`         |
| `--quiet`     | Suprime salida.                   | `systemctl start --quiet unit`            |
| `--wait`      | Espera completado.                | `systemctl start --wait unit`             |
| `--root=`     | Ruta raíz.                        | `systemctl start --root=/path unit`       |
| `--image=`    | Imagen de disco.                  | `systemctl start --image=/path unit`      |
# stop

| Flag           | Descripción       | Ejemplo                                  |
| -------------- | ----------------- | ---------------------------------------- |
| `--no-warn`    | Sin avisos.       | `systemctl stop --no-warn unit`          |
| `--job-mode=`  | Modo de jobs.     | `systemctl stop --job-mode=replace unit` |
| `--no-block`   | Sin esperar.      | `systemctl stop --no-block unit`         |
| `--quiet`      | Suprime salida.   | `systemctl stop --quiet unit`            |
| `--kill-whom=` | Procesos a matar. | `systemctl stop --kill-whom=main unit`   |
| `--signal=`    | Señal a enviar.   | `systemctl stop --signal=SIGTERM unit`   |
# reload

| Flag          | Descripción     | Ejemplo                                    |
| ------------- | --------------- | ------------------------------------------ |
| `--job-mode=` | Modo de jobs.   | `systemctl reload --job-mode=replace unit` |
| `--no-block`  | Sin esperar.    | `systemctl reload --no-block unit`         |
| `--quiet`     | Suprime salida. | `systemctl reload --quiet unit`            |
# restart

| Flag           | Descripción       | Ejemplo                                   |
| -------------- | ----------------- | ----------------------------------------- |
| `--quiet`      | Suprime salida.   | `systemctl restart --quiet unit`          |
| `--no-block`   | Sin esperar.      | `systemctl restart --no-block unit`       |
| `--force`      | Fuerza reinicio.  | `systemctl restart --force unit`          |
| `--kill-whom=` | Procesos a matar. | `systemctl restart --kill-whom=main unit` |
| `--root=`      | Ruta raíz.        | `systemctl restart --root=/path unit`     |
| `--image=`     | Imagen de disco.  | `systemctl restart --image=/path unit`    |
# enable

| Flag        | Descripción            | Ejemplo                               |
| ----------- | ---------------------- | ------------------------------------- |
| `--quiet`   | Suprime salida.        | `systemctl enable --quiet unit`       |
| `--now`     | Inicia tras habilitar. | `systemctl enable --now unit`         |
| `--force`   | Sobrescribe symlinks.  | `systemctl enable --force unit`       |
| `--runtime` | Temporal.              | `systemctl enable --runtime unit`     |
| `--root=`   | Ruta raíz.             | `systemctl enable --root=/path unit`  |
| `--image=`  | Imagen de disco.       | `systemctl enable --image=/path unit` |
# disable

| Flag        | Descripción                | Ejemplo                                |
| ----------- | -------------------------- | -------------------------------------- |
| `--quiet`   | Suprime salida.            | `systemctl disable --quiet unit`       |
| `--now`     | Detiene tras deshabilitar. | `systemctl disable --now unit`         |
| `--force`   | Elimina symlinks.          | `systemctl disable --force unit`       |
| `--runtime` | Temporal.                  | `systemctl disable --runtime unit`     |
| `--root=`   | Ruta raíz.                 | `systemctl disable --root=/path unit`  |
| `--image=`  | Imagen de disco.           | `systemctl disable --image=/path unit` |
# daemon-reload

| Flag       | Descripción      | Ejemplo                                 |
| ---------- | ---------------- | --------------------------------------- |
| `--quiet`  | Suprime salida.  | `systemctl daemon-reload --quiet`       |
| `--root=`  | Ruta raíz.       | `systemctl daemon-reload --root=/path`  |
| `--image=` | Imagen de disco. | `systemctl daemon-reload --image=/path` |
| `--user`   | Servicios custom | `systemctl --user daemon-reload`        |
# is-active

| Flag      | Descripción      | Ejemplo                                   |
| --------- | ---------------- | ----------------------------------------- |
| `--quiet` | Suprime salida.  | `systemctl is-active --quiet unit`        |
| `--type=` | Filtra por tipo. | `systemctl is-active --type=service unit` |
# is-enabled

| Flag      | Descripción     | Ejemplo                             |
| --------- | --------------- | ----------------------------------- |
| `--quiet` | Suprime salida. | `systemctl is-enabled --quiet unit` |
# list-dependencies

| Flag        | Descripción            | Ejemplo                                           |
| ----------- | ---------------------- | ------------------------------------------------- |
| `--reverse` | Dependencias reversas. | `systemctl list-dependencies --reverse unit`      |
| `--after`   | Unidades antes.        | `systemctl list-dependencies --after unit`        |
| `--before`  | Unidades después.      | `systemctl list-dependencies --before unit`       |
| `--all`     | Expande todos.         | `systemctl list-dependencies --all unit`          |
| `--type=`   | Filtra por tipo.       | `systemctl list-dependencies --type=service unit` |
| `--state=`  | Filtra por estado.     | `systemctl list-dependencies --state=active unit` |
| `--plain`   | Lista plana.           | `systemctl list-dependencies --plain unit`        |
# halt

| Flag         | Descripción             | Ejemplo                                    |
| ------------ | ----------------------- | ------------------------------------------ |
| `--quiet`    | Suprime salida.         | `systemctl halt --quiet`                   |
| `--force`    | Sin apagar unidades.    | `systemctl halt --force`                   |
| `--message=` | Mensaje de explicación. | `systemctl halt --message="Mantenimiento"` |
# poweroff

| Flag         | Descripción             | Ejemplo                                  |
| ------------ | ----------------------- | ---------------------------------------- |
| `--quiet`    | Suprime salida.         | `systemctl poweroff --quiet`             |
| `--force`    | Sin apagar unidades.    | `systemctl poweroff --force`             |
| `--message=` | Mensaje de explicación. | `systemctl poweroff --message="Apagado"` |
# reboot.

|Flag|Descripción|Ejemplo|
|---|---|---|
|`--quiet`|Suprime salida.|`systemctl reboot --quiet`|
|`--force`|Sin apagar unidades.|`systemctl reboot --force`|
|`--message=`|Mensaje de explicación.|`systemctl reboot --message="Reinicio"`|
|`--firmware-setup`|Configuración firmware.|`systemctl reboot --firmware-setup`|
