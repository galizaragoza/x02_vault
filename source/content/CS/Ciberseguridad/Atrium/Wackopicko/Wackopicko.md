# Identificación y clasificación
## Crítico
### Cross Site Scripting (XSS)
Se clasifica como crítica debido al daño reputacional y a los usuarios tan grave que puede causar una vulnerabilidad de XSS, el robo de cookies, sesiones, redirección a sitios maliciosos son problemas muy serios y todos ellos son posibles vectores de explotación de XSS.
1. **Caso 1**
La barra de búsqueda de la página es vulnerable a XSS reflejado.
2. **Caso 2**
La caja de comentarios de cada una de las imágenes es vulnerable a ataques de XSS almacenado.
3. **Caso 3** 
El apartado de comentarios de la sección guestbook también es vulnerable a XSS almacenado.
4. **Caso 4**
El parámetro de nombre de archivo a la hora de comprobar en `/piccheck.php` es vulnerable a XSS reflejado.
### SQL injection
El panel de login es vulnerable a bypass de autenticación mediante SQLi. Esto puntúa como crítico ya que a partir de ese punto de fallo se puede llegar a comprometer la información de toda la base de datos.
Un atacante que identifique dicha vulnerabilidad podría manipular, robar, copiar o eliminar información sensible de los usuarios, resultando en daño reputacional y/o económico grave. Además se podría utilizar la información recopilada en la base de datos para infiltrarse aún más en la infraestructura y comprometer de manera aún más grave el servidor.
### Weak credentials
El panel de login de administrador tiene como credenciales `admin:admin`. Es un fallo grave de política de seguridad que tiene fácil arreglo, y es crítico porque cualquier atacante que llege a ese endpoint y pruebe las credenciales débiles más comunes ganará acceso al panel de administrador, dando pie a la vullneración del sistema completo. 
### Path trasversal
En la ruta `http://localhost/admin/index.php?page=login` el parámetro `page` es vulnerable a path trasversal, esto quiere decir que cambiar el valor `login` por un archivo interno del sistema como `/etc/passwd` mostrará el contenido de dicho archivo si se incluye el bypass `%00`, también conocido como null byte.
Un atacante podría exfiltrar información muy sensible de dentro del servidor en un proceso de reconocimiento, para utilizar esa información de manera maliciosa, ya sea para infiltrarse más adentro u otros fines.
### Command injection
El código de la página ejecuta en `http://localhost/passcheck.php` comandos internos del sistema para llevar a cabo la comprobación de la contraseña. Si sustituimos un input bienintencionado como una contraseña por un comando, escapando previamente con un operador de shell como `;` podemos ejecutar comandos en el servidor.
Esto es gravísimo, ya que permite directamente la ejecución de código remoto, dando al atacante control total sobre el servidor, quedando a su merced.
## Grave
### Insecure Direct Object References
El apartado de fotos subidas es vulnerable a IDOR.
### Weak configuration
Hay una ausencia preocupante de Cookies y HTTP Headers de Seguridad que empeoran la seguridad general de la página y facilitan la labor del atacante a la hora de vulnerarla.
### Fallo en la aplicación de cupones
Si se intenta aplicar el mismo cupón, puede observarse que el descuento se puede aplicar una cantidad infinita de veces, haciendo posible que el precio alcance cero. 

<div class="page-break" style="page-break-before: always;"></div>

# Explotación de las vulnerabilidades

### XSS 1
La barra de búsqueda de la página, así como la URL (los campos a través de los cuales enviamos la consulta a la base de datos) son vulnerables a XSS, si o bien inyectamos un payload en el buscador o confeccionamos la URL con uno, veremos que la aplicación tiene una vulnerabilidad de XSS reflejado al ser interpretado el código javascript que va inyectado en dicho payload.

![[XSS-1-1.png|956x394]]

![[XSS-1-2.png]]

---

### XSS 2
Las imágenes subidas por los usuarios tienen una sección de comentarios, si inyectamos en el campo del comentario un payload malicioso de XSS veremos que la aplicación es vulnerable a XSS almacenado.
En este caso hay un paso intermedio de confirmación del comentario. Una vez clicar en el botón 'Create' de este paso intermedio saltará el comentario, probando la existencia de esta vulnerabilidad.

![[XSS-2-1.png|529x349]]

![[XSS-2-2.png|561x143]] ![[XSS-2-3.png|460x142]]

<div class="page-break" style="page-break-before: always;"></div>

### XSS 3 
En la sección de la página "guestbook" tenemos la posibilidad de dejar un comentario anónimo, ya que no requiere login. Con un payload malicioso de XSS inyectado en el campo de comentario veremos que la aplicación es vulnerable a XSS almacenado. 

![[XSS-3-1.png|669x305]] ![[XSS-3-2.png|633x305]]

---

### XSS 4
En la página home tenemos un campo que nos permite la subida de un archivo para comprar si este puede subirse, además tenemos un input de texto para ponerle un nombre. En este input, si inyectamos un payload malicioso de XSS veremos que la aplicación reacciona mostrando una alerta, lo cual es un indicativo de que la aplicación es efectivamente vulnerable a XSS reflejado.

![[XSS-4-1.png]] ![[XSS-4-2.png|1018x504]]



## SQL injection
El panel de login es vulnerable a bypass de autenticación mediante SQLi, esto quiere decir que después de probar una serie de payloads se puede vulnerar el panel de login, burlando la autenticación y entrando como un usuario sin legítimo sin su consentimiento o conocimiento. Esto da pie a tomar acciones en su nombre, gastar sus créditos...

![[SQL-1.png|487x380]] ![[SQL-2.png]]

---

## Weak credentials
Esta vulnerabilidad no es de una gran profundidad técnica pero es igualmente grave. Si un atacante que llega al panel de login para administradores se pusiese a probar credenciales comunes/por defecto, rápidamente se daría cuenta que lo tiene tan sencillo para acceder como probar la combinación `admin:admin`.

![[WK-1.png]] ![[WK-2.png|914x180]]

<div class="page-break" style="page-break-before: always;"></div>


## Insecure Direct Object References
IDOR es una vulnerabilidad que acontece cuando se hacen consultas directas a un objeto de la BBDD se manera insegura, en este caso si visitamos la URL que nos permite ver las imágenes subidas en nuestra propia cuenta `http://192.168.1.150/WackoPicko/users/view.php?userid=1`

![[IDOR-1.png]]

Vemos que con algo tan sencillo como cambiar el id del usuario al que la URL hace referencia, ergo cambiando los parámetros de la query, podremos ver contenido privado de otros usuarios que no se debería poder consultar, ya que requiere pasar por autenticación primero

![[IDOR-2.png]]

<div class="page-break" style="page-break-before: always;"></div>


## Fallo en el cupón
![[couponFlaw.png|655x612]]![[couponFlaw1.png|945x384]]

<div class="page-break" style="page-break-before: always;"></div>


# Prevención, mitigación y buenas prácticas
## Cross Site Scripting (XSS)
La protección contra XSS convencional viene dada a través de capas:
- **Encodear la información en la salida**
Dependiendo del contexto y el lenguaje, se deben aplicar en el código distintos métodos de codificación para que los inputs de usuario viajen de forma segura hacia el back-end
- **Validar el input del usuario**
Encodear no es suficiente, debido a los distintos métodos de ofuscación y bypass, por este motivo es necesario también validar de la forma más estricta posible que los inputs del usuario no tienen intenciones maliciosas, por ejemplo
	1. Si un usuario aporte un valor que debería ser númerico, validar que se trata de un entero y no otro tipo de variable
	2. Bloquear cierto tipo de caracteres cuando no sean requeridos
- **Whitelists y blacklists**
Una whitelist es una lista de cosas que SÍ se permiten, mientras que una blacklist de una lista de NO están permitidas, ya sea en forma de IPs, protocolos o caracteres, es buena práctica implementar este tipo de listas.

```php
<?php our_header(); ?>

<div class="column prepend-1 span-14 first" >
   <h2 id="image-title"><?=h( $pic['title'] )?> </h2>
	<img id="image" src="../upload/<?=h( $pic['filename'] )?>.550.jpg" width="550" />
	
	<div class="column span-14 first last " id="comments">
	  <div class="column span-14 first last">
	    <h2 id="comment-title">Comments</h2>
	  </div>
   <?php if ($comments) {
   foreach ($comments as $comment) {  ?>
	  <div class="column prepend-1 span-12 first last">
	    <p class="comment"><?= $comment['text'] ?></p>
	  </div>
	  <div class="column prepend-10 span-6 first last">
	  - by <a href="<?= Users::$VIEW_URL ?>?userid=<?=h( $comment['user_id'] )?>"><?=h( $comment['login'] ) ?></a>
	  </div>
<?php
   }
}

else
{
?>	  
   <div class="column prepend-1-span12 first last" style="padding-top:2em;padding-bottom:2em">
      <h3 style="text-align:center">No comments yet...</h3>
      </div>
<?php
      }

?>
	  <div class="column prepend-1 span-12 first last" style="padding-bottom:2em;">
	    <h3 style="text-align:center;padding-top:2em;">Add your comment</h3>
	    <form action="<?= Comments::$PREVIEW_COMMENT_URL ?>" method="POST">
	      <textarea id="comment-box" name="text"></textarea>
	      <div class="column prepend-9 first last">
		<input type="hidden" name="picid" value="<?=h( $pic['id'] )?>"/>
		<input type="submit" value="Preview"/>

	      </div>
	    </form>
	  </div>
	  
	</div>
      </div>
      
      <div class="column prepend-2 append-1 span-6 last" style="padding-top:1em;" >
   <h3 id="tradebux"><?=h( $pic['price'] )?> Tradebux<br />
   <?php $usr = Users::current_user(); if ($usr['id'] != $pic['user_id']) { ?>
	  <a href="<?=h( Cart::$ACTION_URL . '?action=add&picid=' . $pic['id'] );?>">Add to Cart</a>
    <?php } ?>
	</h3>
	<h3 id="info-area">Uploaded on <?=h( date("F j, Y", $pic['created_on_unix']) )?><br />
					  by <a href="<?= Users::$VIEW_URL ?>?userid=<?=h( $pic['user_id'] ) ?>"><?=h($pic['login']) ?></a>
	</h3>  
	<?php if ($related) { ?>					  
	<div class="column span-6 first last" id="related">
	  <div class="column span-6 first last">
	    <h2 id="related-title">Related</h2>
	  </div>
      <?php foreach ($related as $p) { ?>
	  <div class="column prepend-1 span-4 first last">
	    <a href="<?=h( Pictures::$VIEW_PIC_URL . "?picid=" . $p['id'] ) ?>"><img src="/upload/<?=h($p['filename']) ?>.128.jpg" width="128" /></a>
	 <p>by <a href="<?= Users::$VIEW_URL ?>?userid=<?=h( $p['user_id'] )?>"><?=h( $p['login'] )?></a></p>
	  </div>
<?php }?>
	</div>
	<?php } ?>
	<?php if ($same) { ?>
	<div class="column span-6 first last" id="same-upload">
	  <div class="column span-6 first last">
	    <h2 id="same-upload-title">Other by <a href="<?= Users::$VIEW_URL ?>?userid=<?=h( $pic['user_id'] )?>"><?=h($pic['login']) ?></a></h2>
	  </div>

      <?php foreach ($same as $pic) { ?>
	  <div class="column prepend-1 span-4 first last" style="margin-bottom:2em;">
	    <a href="<?=h( Pictures::$VIEW_PIC_URL . "?picid=" . $pic['id'] ) ?>"><img src="/upload/<?=h($pic['filename']) ?>" width="128" /></a>
	  </div>
<?php }?>
	</div>
	<?php } ?>
	
      </div>



<?php our_footer(); ?>
```

En este fragmento del código de la página en el cual se muestran los comentarios, también está el código que permite la creación de uno nuevo. El input de usuario se acepta sin sanitización y esto es lo que permite la inyección de código, resultando en la vulnerabilidad XSS.

En esta segunda versión se han hecho ciertos cambios para solventar este problema

```php
<?php our_header(); ?>

<div class="column prepend-1 span-14 first" >
   <h2 id="image-title"><?= htmlspecialchars($pic['title'], ENT_QUOTES, 'UTF-8') ?></h2> <!-- Cambio: Reemplazado h() por htmlspecialchars() con ENT_QUOTES y UTF-8 para escapar caracteres especiales y prevenir XSS -->
   <img id="image" src="../upload/<?= htmlspecialchars($pic['filename'], ENT_QUOTES, 'UTF-8') ?>.550.jpg" width="550" /> 
   // Cambio: Escapado de filename para evitar breakout de atributos
   
   <div class="column span-14 first last" id="comments">
      <div class="column span-14 first last">
         <h2 id="comment-title">Comments</h2>
      </div>
      <?php if ($comments) {
         foreach ($comments as $comment) { ?>
            <div class="column prepend-1 span-12 first last">
               <p class="comment"><?= htmlspecialchars($comment['text'], ENT_QUOTES, 'UTF-8') ?></p> 
               // Cambio: Escapado del texto del comentario para prevenir inyección de scripts (principal vulnerabilidad XSS)
            </div>
            <div class="column prepend-10 span-6 first last">
               - by <a href="<?= htmlspecialchars(Users::$VIEW_URL . '?userid=' . $comment['user_id'], ENT_QUOTES, 'UTF-8') ?>"><?= htmlspecialchars($comment['login'], ENT_QUOTES, 'UTF-8') ?></a> 
                 // Cambio: Escapado de URL y login para prevenir XSS en atributos y contenido
            </div>
      <?php }
      } else { ?>
         <div class="column prepend-1-span12 first last" style="padding-top:2em;padding-bottom:2em">
            <h3 style="text-align:center">No comments yet...</h3>
         </div>
      <?php } ?>
      <div class="column prepend-1 span-12 first last" style="padding-bottom:2em;">
         <h3 style="text-align:center;padding-top:2em;">Add your comment</h3>
         <form action="<?= htmlspecialchars(Comments::$PREVIEW_COMMENT_URL, ENT_QUOTES, 'UTF-8') ?>" method="POST"> 
         // Cambio: Escapado de la URL del formulario
            <textarea id="comment-box" name="text"></textarea>
            <div class="column prepend-9 first last">
               <input type="hidden" name="picid" value="<?= htmlspecialchars($pic['id'], ENT_QUOTES, 'UTF-8') ?>"/> 
               // Cambio: Escapado del ID de la imagen
               <input type="submit" value="Preview"/>
            </div>
         </form>
      </div>
   </div>
</div>

<div class="column prepend-2 append-1 span-6 last" style="padding-top:1em;" >
   <h3 id="tradebux"><?= htmlspecialchars($pic['price'], ENT_QUOTES, 'UTF-8') ?> Tradebux<br /> 
   // Cambio: Escapado del precio
   <?php $usr = Users::current_user(); if ($usr['id'] != $pic['user_id']) { ?>
      <a href="<?= htmlspecialchars(Cart::$ACTION_URL . '?action=add&picid=' . $pic['id'], ENT_QUOTES, 'UTF-8') ?>">Add to Cart</a> 
      // Cambio: Escapado de la URL del carrito
   <?php } ?>
   </h3>
   <h3 id="info-area">Uploaded on <?= htmlspecialchars(date("F j, Y", $pic['created_on_unix']), ENT_QUOTES, 'UTF-8') ?><br /> 
   // Cambio: Escapado de la fecha
      by <a href="<?= htmlspecialchars(Users::$VIEW_URL . '?userid=' . $pic['user_id'], ENT_QUOTES, 'UTF-8') ?>"><?= htmlspecialchars($pic['login'], ENT_QUOTES, 'UTF-8') ?></a> 
     // Cambio: Escapado de URL y login
   </h3>  
   <?php if ($related) { ?>					  
      <div class="column span-6 first last" id="related">
         <div class="column span-6 first last">
            <h2 id="related-title">Related</h2>
         </div>
         <?php foreach ($related as $p) { ?>
            <div class="column prepend-1 span-4 first last">
               <a href="<?= htmlspecialchars(Pictures::$VIEW_PIC_URL . "?picid=" . $p['id'], ENT_QUOTES, 'UTF-8') ?>"><img src="/upload/<?= htmlspecialchars($p['filename'], ENT_QUOTES, 'UTF-8') ?>.128.jpg" width="128" /></a> <!-- Cambio: Escapado de URL y filename -->
               <p>by <a href="<?= htmlspecialchars(Users::$VIEW_URL . '?userid=' . $p['user_id'], ENT_QUOTES, 'UTF-8') ?>"><?= htmlspecialchars($p['login'], ENT_QUOTES, 'UTF-8') ?></a></p>
               // Cambio: Escapado de URL y login
            </div>
         <?php } ?>
      </div>
   <?php } ?>
   <?php if ($same) { ?>
      <div class="column span-6 first last" id="same-upload">
         <div class="column span-6 first last">
            <h2 id="same-upload-title">Other by <a href="<?= htmlspecialchars(Users::$VIEW_URL . '?userid=' . $pic['user_id'], ENT_QUOTES, 'UTF-8') ?>"><?= htmlspecialchars($pic['login'], ENT_QUOTES, 'UTF-8') ?></a></h2>
            // Cambio: Escapado de URL y login
         </div>
         <?php foreach ($same as $pic) { ?>
            <div class="column prepend-1 span-4 first last" style="margin-bottom:2em;">
               <a href="<?= htmlspecialchars(Pictures::$VIEW_PIC_URL . "?picid=" . $pic['id'], ENT_QUOTES, 'UTF-8') ?>"><img src="/upload/<?= htmlspecialchars($p['filename'], ENT_QUOTES, 'UTF-8') ?>.128.jpg" width="128" /></a>
               // Cambio: Escapado de URL y filename
            </div>
         <?php } ?>
      </div>
   <?php } ?>
</div>

<?php our_footer(); ?>
```

También se pueden tomar medidas adicionales (muy recomendable) como Headers de seguridad o templates.

<div class="page-break" style="page-break-before: always;"></div>


## SQL injection
### Prevención
Hay varias medidas para prevenir ataques de este tipo:
- Validación de entradas del usuario (sanitización) --> Es importante desconfiar de todo lo que entra al sistema desde inputs del usuario, ya que un atacante puede aprovechar esta falta de validación para vulnerar la aplicación.
- Firewalls que filtren las queries SQL maliciosas
```php
<?php

require_once("../include/users.php");
require_once("../include/html_functions.php");
require_once("../include/functions.php");

session_start();

$error = False;
if (isset($_POST['firstname']) && isset($_POST['username']) && isset($_POST['password']) && isset($_POST['againpass']) && isset($_POST['lastname'])
    && $_POST['username'] && $_POST['password'] && $_POST['againpass'] && $_POST['firstname'] && $_POST['lastname'])
{
   if ($_POST['password'] != $_POST['againpass'])
   {
      $flash['error'] = "The passwords do not match. Try again";
      $error = True;
   }
   else if ($new_id = Users::create_user($_POST['username'], $_POST['password'], $_POST['firstname'], $_POST['lastname'], False))
   {
      Users::login_user($new_id);
      http_redirect(Users::$HOME_URL);
   }
   else
   {
      if (mysql_errno() == 1062)
      {
	 $flash['error'] = "Username '{$_POST['username']}' is already in use.";
      }
      $error = True;
   }
}
else
{
   $flash['error'] = "All fields are required";
   $error = True;
}

if ($error)
{
   our_header();
   ?>
<div class="column prepend-1 span-24 first last" >
<h2> Register for an account!</h2>
<p>
Protect yourself from hackers and <a href="/passcheck.php">check your password strength</a>
</p>
<?php error_message() ?>
<table cellspacing="0" style="width:320px">
  <form action="<?=h( $_SERVER['PHP_SELF'] )?>" method="POST">
  <tr><td>Username :</td><td> <input type="text" name="username" /></td></tr>
  <tr><td>First Name :</td><td> <input type="text" name="firstname" /></td></tr>
  <tr><td>Last Name :</td><td> <input type="text" name="lastname" /></td></tr>
  <tr><td>Password :</td><td> <input type="password" name="password" /></td></tr>
  <tr><td>Password again :</td><td> <input type="password" name="againpass" /></td></tr>
  <tr><td><input type="submit" value="Create Account!" /></td><td></td></tr>
</form>
</table>
</div>



<?php
     our_footer();
}

?>
```

En este código que gestiona el registro de los usuarios hay varias fallas que permiten la explotación de SQL injection, la versión bajo esto es una corregida con una serie de buenas prácticas para evitar ser atacado mediante SQLi

```php
<?php
require_once("../include/users.php");
require_once("../include/html_functions.php");
require_once("../include/functions.php");

session_start();

$error = False;
if (isset($_POST['firstname']) && isset($_POST['username']) && isset($_POST['password']) && isset($_POST['againpass']) && isset($_POST['lastname'])
    && $_POST['username'] && $_POST['password'] && $_POST['againpass'] && $_POST['firstname'] && $_POST['lastname'])
{
   if ($_POST['password'] != $_POST['againpass'])
   {
      $flash['error'] = "The passwords do not match. Try again";
      $error = True;
   }
   else
   {
      // Cambio: Validar formato de username (solo letras, números, guiones bajos, max 50 caracteres)
      if (!preg_match('/^[a-zA-Z0-9_]{1,50}$/', $_POST['username']))
      {
         $flash['error'] = "Invalid username format.";
         $error = True;
      }
      else
      {
         // Cambio: Asumiendo que Users::create_user usa PDO con prepared statements
         // Se espera que el método ahora use consultas parametrizadas para prevenir SQLi
         $hashed_password = password_hash($_POST['password'], PASSWORD_DEFAULT); // Cambio: Hashear contraseña
         $new_id = Users::create_user($_POST['username'], $hashed_password, $_POST['firstname'], $_POST['lastname'], False);
         if ($new_id)
         {
            Users::login_user($new_id);
            http_redirect(Users::$HOME_URL);
         }
         else
         {
            // Cambio: Manejo de error genérico para duplicados sin exponer detalles de la DB
            $flash['error'] = "Username already in use.";
            $error = True;
         }
      }
   }
}
else
{
   $flash['error'] = "All fields are required";
   $error = True;
}

if ($error)
{
   our_header();
   ?>
<div class="column prepend-1 span-24 first last" >
<h2> Register for an account!</h2>
<p>
Protect yourself from hackers and <a href="/passcheck.php">check your password strength</a>
</p>
<?php error_message() ?>
<table cellspacing="0" style="width:320px">
  <form action="<?= htmlspecialchars($_SERVER['PHP_SELF'], ENT_QUOTES, 'UTF-8') ?>" method="POST">
  <tr><td>Username :</td><td> <input type="text" name="username" /></td></tr>
  <tr><td>First Name :</td><td> <input type="text" name="firstname" /></td></tr>
  <tr><td>Last Name :</td><td> <input type="text" name="lastname" /></td></tr>
  <tr><td>Password :</td><td> <input type="password" name="password" /></td></tr>
  <tr><td>Password again :</td><td> <input type="password" name="againpass" /></td></tr>
  <tr><td><input type="submit" value="Create Account!" /></td><td></td></tr>
</form>
</table>
</div>
<?php
   our_footer();
}
?>
```

### Mitigación
- Criptografía: Hay que asegurar que toda la información, o al menos la sensible, esta correctamente protegida mediante métodos criptográficos para que en caso de sufrir una brecha, el atacante no tenga acceso a los datos en texto plano
- Firewalls: Se pueden configurar los firewalls para que bloqueen cierto tipo de queries SQL.

<div class="page-break" style="page-break-before: always;"></div>


## Weak credentials
### Prevención
Para prevenir ataques que se aprovechen de una vulnerabilidad de este tipo, hay que seguir una serie de pautas respecto a las credenciales con las que nos autenticamos con el fin de asegurar la aplicación:
- Cambiar en cuanto sea posible las credenciales por defecto de cualquier servicio
- Aplicar claves robustas, de mínimo 12 caracteres y variadas (minúsculas, mayúsculas, números, caracteres especiales) y usar gestores de contraseñas
- Rotar las claves a menudo y no almacenarlas nunca en texto plano en lugares no seguros
### Mitigación
Para mitigar un ataque de este tipo hay que tener ciertas medidas de seguridad:
- Avisos por cada intento de login
- Autenticación de doble factor o multi factor

---

## Insecure Direct Object References
### Prevención
Para prevenir ataques de este tipo:
- A no ser que un recurso esté pensado para poder ser accedido públicamente, aplicar el principio de negar por defecto
- Jamás confiar exclusivamente en la ofuscación como método de control de acceso; no caer en la trampa de *security-by-obscurity*.
- Utilizar un único sistema de control de acceso a nivel aplicación
- Negar y desconfiar por defecto, definir bien en la fase de desarrollo quién debería tener acceso a qué recursos

```php
<?php

$usercheck = False;
include("view.php");

?>
```

Este es el código que muestra la página de cada usuario, y es vulnerable a IDOR, esto quiere decir que algo tan sencillo como cambiar el parámetro que la página toma para saber que información mostrar (el identificador de usuario) por otro parámetro válido, nos mostrará información privada de otro usuario que no deberíamos poder ver.
La versión de abajo está corregida con una serie de pasos de autenticación y autorización.

```php
<?php
require_once("../include/users.php"); // Cambio: Incluir dependencias necesarias

session_start(); // Cambio: Asegurar que la sesión esté iniciada

// Cambio: Verificar si el usuario está autenticado
if (!Users::current_user()) {
    http_response_code(401); // No autorizado
    exit("Please log in to view user profiles.");
}

// Cambio: Validar y sanitizar el parámetro userid
$requested_userid = isset($_GET['userid']) ? (int)$_GET['userid'] : Users::current_user()['id'];
if ($requested_userid <= 0) {
    http_response_code(400); // Solicitud inválida
    exit("Invalid user ID.");
}

// Cambio: Verificar autorización (solo el usuario o admin puede ver el perfil)
$current_user = Users::current_user();
if ($current_user['id'] !== $requested_userid && !$current_user['is_admin']) {
    http_response_code(403); // Acceso denegado
    exit("You are not authorized to view this profile.");
}

// Cambio: Pasar el userid validado a view.php de forma segura
$_GET['userid'] = $requested_userid; // Asegurar que view.php use el ID validado
include("view.php"); // Incluir vista solo si pasa las verificaciones
?>
```

Al diseñar un sitio web es crítico tener en cuenta siempre los principios AAA (Authentication, Authorization y Accountability), y aplicarlos e implementarlos de manera correcta. 
Autorización es el principio que falla en este caso, y se basa en la idea de tener una política muy clara de que recursos hay en una página, y permisos tienen los usuarios respecto a esos recursos. Si mi página de usuario sólo debería poder verla yo, hay que escribir el código asegurando que ese recurso solo puede ser accedido por los usuarios legítimos.

<div class="page-break" style="page-break-before: always;"></div>


## Path trasversal

```php
<?php

$page = $_GET['page'] . '.php';
require_once($page);

?>
```

Este código es vulnerable a path trasversal, aplicando unas medidas mínimas de seguridad en el ejemplo de abajo se ve como impedir que un atacante pueda recuperar archivos sensibles internos del servidor.

```php
<?php
// Cambio: Definir una lista de páginas permitidas para prevenir inclusión de archivos no autorizados
$allowed_pages = ['home', 'about', 'contact', 'gallery'];

// Cambio: Validar y sanitizar el parámetro 'page' para evitar path traversal
$page = isset($_GET['page']) ? basename($_GET['page']) : 'home'; // Usar 'home' como página por defecto

// Cambio: Verificar si la página está en la lista de permitidas
if (!in_array($page, $allowed_pages)) {
    http_response_code(404); // Cambio: Devolver error 404 si la página no es válida
    exit("Page not found");
}

// Cambio: Construir la ruta del archivo de forma segura, asegurando que solo se incluyan archivos .php
$page_path = __DIR__ . '/' . $page . '.php';

// Cambio: Verificar que el archivo existe antes de incluirlo
if (!file_exists($page_path)) {
    http_response_code(404); // Cambio: Devolver error 404 si el archivo no existe
    exit("Page not found");
}

require_once($page_path);
?>
```

<div class="page-break" style="page-break-before: always;"></div>


## Command injection

``` php
<?php
require_once("../include/html_functions.php");

if (!isset($_GET["password"])) // ?password=blah
{
   error_404();
}
$pass = $_GET["password"];


exec("/bin/cat /usr/share/dict/words | grep " . $pass, $output, $status); // MUY VULNERABLE

if ($status == 0)
{
   $strong = False;
}
else
{
   $string = True;
}


?>

<?php our_header("home"); ?>

<div class="column prepend-1 span-24 first last">
<h2>Password Strength</h2>
<p>
   <?php if ($strong) { ?>
   <h3>You have chosen a strong password.</h3>
   <?php } else { ?>
   <h3>You have chosen a weak password.</h3>
   <?php } ?>
    
</p>
</div>
```

Este código sacado del endpoint `/users/passcheck.php` permite la ejecución de código por la línea comentada, que ejecuta directamente un comando del sistema con sanitización insuficiente.
La versión bajo este comentario implementa varias medidas de securización que permiten mantener la funcionalidad sin sacrificar seguridad.

``` php
<?php
require_once("../include/html_functions.php");

if (!isset($_GET["password"])) // ?password=blah
{
   error_404();
}

// Cambio: Sanitizar y escapar el input para prevenir inyección de comandos
$pass = escapeshellarg($_GET["password"]);

// Cambio: Usar una lista de palabras permitida en lugar de grep para evitar ejecución de comandos arbitrarios
// Simula la verificación de si la contraseña está en un diccionario
$wordlist = file('/usr/share/dict/words', FILE_IGNORE_NEW_LINES | FILE_SKIP_EMPTY_LINES);
$strong = !in_array(trim($_GET["password"]), $wordlist); // Cambio: Comparar directamente sin ejecutar comandos

?>

<?php our_header("home"); ?>

<div class="column prepend-1 span-24 first last">
<h2>Password Strength</h2>
<p>
   <?php if ($strong) { ?>
   <h3>You have chosen a strong password.</h3>
   <?php } else { ?>
   <h3>You have chosen a weak password.</h3>
   <?php } ?>
</p>
</div>

<?php our_footer(); ?>
```

<div class="page-break" style="page-break-before: always;"></div>


## Fallo en los cupones

```php
<?php

require_once("../include/users.php");
require_once("../include/pictures.php");
require_once("../include/cart.php");
require_once("../include/html_functions.php");
require_once("../include/functions.php");


session_start();
require_login();

$user = Users::current_user();

$cart = Cart::get_cart($user['id']);

if ($cart)
{
   $items = Cart::cart_items($cart['id']);
   $coupons = Cart::cart_coupons($cart['id']);
}
our_header("cart");
?>
<div class="column prepend-1 span-22 first last">
  <h2>Welcome to your cart <?= h( $user['login'] ) ?></h2>
  <form action="<?=h( Cart::$ACTION_URL . '?action=delete' ); ?>" method="POST">
<?php if ($cart) { ?>
  <table>
    <tr>
      <th>Pic name</th> <th>Sample Pic</th> <th>Price</th> <th>Delete?</th>
    </tr>
    <?php foreach($items as $item) { ?>
    <tr>
      <td><?=h($item['title']); ?></td> <td><img src="../upload/<?=h( $item['filename'] );?>" alt="<?=h( $item['title'] );?>" height="<?=h( $item['height'] );?>" width="<?=h( $item['width'] );?>" /></td><td><?=h( $item['price'] );?> Tradebux</td> <td><input type="checkbox" value="<?=h( $item['picture_id'] );?>" name="pics[]" /> </td>
    </tr>
    <?php } ?>
  </table>			         
  <?php if ($coupons) { ?>
  <table>
    <tr>
      <th>Coupon Code</th> <th>Coupon Amount</th>
    </tr>
    <?php foreach($coupons as $coupon) { ?>
    <tr>
      <td><?=h($coupon['code']) ?></td><td><?=h( 100.0 - $coupon['discount']) ?>% Off</td>
    </tr>
    <?php } ?>
  </table>
  <?php } ?>
  <input type="submit" value="Remove From Cart" />
</form>
<form action="<?=h( Cart::$ACTION_URL . '?action=addcoupon' ) ?>" method="POST">
Enter Coupon Code: <input type="text" name="couponcode" /><br />
<input type="submit" value="Submit Coupon" />
</form>

<a href="<?=h( Cart::$CONFIRM_URL );?>">Continue to Confirmation</a>
<?php } 
else { ?>
<h2> You don't have a cart! </h2>
<p><a href="<?=h (Pictures::$RECENT_URL)?>">Go find some pictures!</a></p>
<?php } ?>
</div>
<?php
our_footer();
?>
```

Este código del endpoint `/cart/review.php` permite aplicar los cupones una cantidad infinita de veces, llegando a alcanzar un precio de 0.
Este versión corregida contiene una función muy simple que comprueba si ya se ha aplicado un cupón y no permite aplicarlo de nuevo de ser el caso.

```php
<?php
require_once("../include/users.php");
require_once("../include/pictures.php");
require_once("../include/cart.php");
require_once("../include/html_functions.php");
require_once("../include/functions.php");

session_start();
require_login();

$user = Users::current_user();
$cart = Cart::get_cart($user['id']);

if ($cart) {
   $items = Cart::cart_items($cart['id']);
   $coupons = Cart::cart_coupons($cart['id']);
}

our_header("cart");
?>
<div class="column prepend-1 span-22 first last">
  <h2>Welcome to your cart <?= htmlspecialchars($user['login'], ENT_QUOTES, 'UTF-8') ?></h2>
  <form action="<?= htmlspecialchars(Cart::$ACTION_URL . '?action=delete', ENT_QUOTES, 'UTF-8') ?>" method="POST">
<?php if ($cart) { ?>
  <table>
    <tr>
      <th>Pic name</th> <th>Sample Pic</th> <th>Price</th> <th>Delete?</th>
    </tr>
    <?php foreach($items as $item) { ?>
    <tr>
      <td><?= htmlspecialchars($item['title'], ENT_QUOTES, 'UTF-8') ?></td>
      <td><img src="../upload/<?= htmlspecialchars($item['filename'], ENT_QUOTES, 'UTF-8') ?>" alt="<?= htmlspecialchars($item['title'], ENT_QUOTES, 'UTF-8') ?>" height="<?= htmlspecialchars($item['height'], ENT_QUOTES, 'UTF-8') ?>" width="<?= htmlspecialchars($item['width'], ENT_QUOTES, 'UTF-8') ?>" /></td>
      <td><?= htmlspecialchars($item['price'], ENT_QUOTES, 'UTF-8') ?> Tradebux</td>
      <td><input type="checkbox" value="<?= htmlspecialchars($item['picture_id'], ENT_QUOTES, 'UTF-8') ?>" name="pics[]" /></td>
    </tr>
    <?php } ?>
  </table>			         
  <?php if ($coupons) { ?>
  <table>
    <tr>
      <th>Coupon Code</th> <th>Coupon Amount</th>
    </tr>
    <?php foreach($coupons as $coupon) { ?>
    <tr>
      <td><?= htmlspecialchars($coupon['code'], ENT_QUOTES, 'UTF-8') ?></td>
      <td><?= htmlspecialchars(100.0 - $coupon['discount'], ENT_QUOTES, 'UTF-8') ?>% Off</td>
    </tr>
    <?php } ?>
  </table>
  <?php } ?>
  <input type="submit" value="Remove From Cart" />
</form>
<form action="<?= htmlspecialchars(Cart::$ACTION_URL . '?action=addcoupon', ENT_QUOTES, 'UTF-8') ?>" method="POST">
<?php
// Cambio: Verificar si el carrito ya tiene un cupón aplicado
if ($coupons && count($coupons) > 0) {
    echo '<p style="color:red;">Only one coupon can be applied per cart.</p>';
} else {
?>
Enter Coupon Code: <input type="text" name="couponcode" pattern="[A-Za-z0-9]{1,20}" required title="Coupon code must be alphanumeric, up to 20 characters" /><br />
<!-- Cambio: Agregar validación del formato del cupón en el cliente (alphanumeric, max 20 caracteres) -->
<input type="submit" value="Submit Coupon" />
<?php } ?>
</form>

<a href="<?= htmlspecialchars(Cart::$CONFIRM_URL, ENT_QUOTES, 'UTF-8') ?>">Continue to Confirmation</a>
<?php } else { ?>
<h2>You don't have a cart!</h2>
<p><a href="<?= htmlspecialchars(Pictures::$RECENT_URL, ENT_QUOTES, 'UTF-8') ?>">Go find some pictures!</a></p>
<?php } ?>
</div>
<?php
our_footer();
?>
```

<div class="page-break" style="page-break-before: always;"></div>


# Buenas prácticas generales

- **Principio de Zero Trust**: Desconfiar por defecto de todas las solicitudes exteriores a la red confiada de la infraestructura
	- Política de denegar por defecto: En la línea de zero trust, desconfiar de todo input externo y negar por defecto. Solo permitir input verificado y validado
- **Principio de Seguridad por Defecto**: La seguridad no debe ser un objetivo a conseguir en algún punto del desarrollo, debe ser uno de los cimientos sobre los que se construye la aplicación
	- Política de privacidad como filosofía de diseño
- **Principios de autenticación, autorización, y responsabilidad (AAA)**: Autenticar a los usuarios, tener buenas políticas de acceso y guardar registros de todo
	- Buenas políticas de contraseñas (rotación, MFA...)
- **Auditorías frecuentes**: Entre 1 y 4 veces al año contratar una auditoría externa
- **Software/hardware perimetral de seguridad**: Firewalls, IDSs, IPSs, VPNs...
- **Parcheo y mantenimiento frecuente**
- **Copias de seguridad**
