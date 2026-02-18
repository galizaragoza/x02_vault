### ¿Qué es IDOR?
IDOR o Insecure Direct Object References (Referencias Directas a Objetos Inseguras), es una vulnerabilidad que surge cuando una aplicación web accede a recursos de forma directa a partir del input del usuario. Por ejemplo, imaginemos que clicar en el icono de una casa nos lleva a nuestro perfil, y la URL tiene este aspecto:
```
https://sitio-inseguro/usuario?usario_numero=1234
```
El input del usuario está provocando una consulta directa a la base de datos a través de su ID, si no hay defensas para ello, un atacante podría simplemente probar distintos números y ver información de otros clientes, saltándose la autenticación. Esto se conoce como horizontal privilege escalation.
Esto podría llevar a la exfiltración de contraseñas o incluso hacer cambios en nombre del asociado legítimamente al ID que se está probando.

### UID
https://youtu.be/EQ7QdlXV6fo?list=PL65_wYSEg5HdSq1pSZxQmz7SoNE48vOgR&t=170

### Mitigación y prevención
##### Prevención
Para prevenir ataques de este tipo:
- A no ser que un recurso esté pensado para poder ser accedido públicamente, aplicar el principio de negar por defecto
- Jamás confiar exclusivamente en la ofuscación como método de control de acceso; no caer en la trampa de *security-by-obscurity*.
- Utilizar un único sistema de control de acceso a nivel aplicación
- Negar y desconfiar por defecto, definir bien en la fase de desarrollo quién debería tener acceso a qué recursos
```php
<?php
// PERFIL DE USUARIO VULNERABLE
session_start();

// 🚨 ID EXPUESTO EN URL - SIN VERIFICACIÓN
$user_id = $_GET['id'];

// CONEXIÓN A BD
$conn = new mysqli("localhost", "root", "", "app");

// 🚨 CONSULTA SIN VERIFICAR PERMISOS
$query = "SELECT * FROM users WHERE id = $user_id";
$result = $conn->query($query);

if ($result->num_rows > 0) {
    $user = $result->fetch_assoc();
    echo "<h1>Perfil de: " . htmlspecialchars($user['username']) . "</h1>";
    echo "<p>Email: " . htmlspecialchars($user['email']) . "</p>";
    echo "<p>Teléfono: " . htmlspecialchars($user['phone']) . "</p>";
    // 🚨 DATOS SENSIBLES EXPUESTOS
} else {
    echo "Usuario no encontrado";
}
?>

<!-- 🚨 ENLACES CON IDs EXPUESTOS -->
<a href="profile.php?id=1">Mi Perfil</a>
<a href="profile.php?id=2">Perfil de Ana</a>
<a href="profile.php?id=3">Perfil de Carlos</a>
```
Este script es vulnerable a IDOR debido a su falta de autenticación y control de acceso, falta de verificación de permisos y mala gestión de los IDs(expuesto en la URL)
```php
<?php
session_start();

// 🛡️ VERIFICAR SI EL USUARIO ESTÁ AUTENTICADO
if (!isset($_SESSION['user_id'])) {
    header("Location: login.php");
    exit();
}

// 🛡️ USAR ID DE SESIÓN, NO PARÁMETRO URL
$current_user_id = $_SESSION['user_id'];

// CONEXIÓN SEGURA
try {
    $conn = new PDO("mysql:host=localhost;dbname=app;charset=utf8", "app_user", "StrongPass123!");
    $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    
    // 🛡️ SOLO PERMITIR VER EL PROPIO PERFIL
    $query = "SELECT username, email, phone FROM users WHERE id = :user_id";
    $stmt = $conn->prepare($query);
    $stmt->bindParam(':user_id', $current_user_id, PDO::PARAM_INT);
    $stmt->execute();
    
    if ($stmt->rowCount() === 1) {
        $user = $stmt->fetch(PDO::FETCH_ASSOC);
        echo "<h1>Mi Perfil</h1>";
        echo "<p>Usuario: " . htmlspecialchars($user['username']) . "</p>";
        echo "<p>Email: " . htmlspecialchars($user['email']) . "</p>";
        echo "<p>Teléfono: " . htmlspecialchars($user['phone']) . "</p>";
    } else {
        throw new Exception("Usuario no encontrado");
    }
    
} catch (Exception $e) {
    error_log("IDOR attempt detected: " . $e->getMessage());
    echo "Error al cargar el perfil";
}
?>

<!-- 🛡️ ENLACE SEGURO SIN PARÁMETROS -->
<a href="my_profile.php">Mi Perfil</a>
```
En cambio, esta versión tiene mejoras que aportan a la seguridad, verifica si el usuario está o no autenticado, usa el ID de la sesión en lugar de parámetro URL, securiza la conexión a la DB y solo permite ver el perfil propio

## Recursos
[Explotación IDOR](https://youtu.be/EQ7QdlXV6fo?list=PL65_wYSEg5HdSq1pSZxQmz7SoNE48vOgR)
[IDOR CheatSheet](https://jasper-join-7e5.notion.site/IDOR-Cheat-Sheet-9f7c9e3285bf4c7cae5ea38243cd0391)
### Fuentes
[OWASP](https://cheatsheetseries.owasp.org/cheatsheets/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet.html)
[PortSwigger](https://portswigger.net/web-security/access-control/idor)
[PATT](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Insecure%20Direct%20Object%20References#methodology)
