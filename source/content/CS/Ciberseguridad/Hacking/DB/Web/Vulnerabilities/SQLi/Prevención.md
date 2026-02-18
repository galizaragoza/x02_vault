# Prevención
Hay varias medidas para prevenir ataques de este tipo:
- Validación de entradas del usuario (sanitización) --> Es importante desconfiar de todo lo que entra al sistema desde inputs del usuario, ya que un atacante puede aprovechar esta falta de validación para vulnerar la aplicación.
- Firewalls que filtren las queries SQL maliciosas

```php
<?php
// CONEXIÓN A LA BD
$host = "localhost";
$user = "root";
$pass = "";
$db = "usuarios";
$conn = new mysqli($host, $user, $pass, $db);

// RECEPCIÓN DE DATOS SIN VALIDACIÓN
$username = $_POST['username'];
$password = $_POST['password'];

// 🚨 QUERY VULNERABLE - CONCATENACIÓN DIRECTA
$query = "SELECT * FROM users WHERE username = '$username' AND password = '$password'";
$result = $conn->query($query);

if ($result->num_rows > 0) {
    echo "Login exitoso!";
} else {
    echo "Credenciales incorrectas";
}
?>
```

```html
<form method="POST" action="login.php">
    <input type="text" name="username" placeholder="Usuario" required>
    <input type="password" name="password" placeholder="Contraseña" required>
    <button type="submit">Ingresar</button>
</form>
```

Este código es un ejemplo de como **NO** hacer un formulario, el script toma los datos tal como entrar desde el formulario y los pasa en forma de query a la base de datos, y el formulario no sanitiza la entrada.

```php
<?php
// CONEXIÓN SEGURA A LA BD
$host = "localhost";
$user = "app_user"; // Usuario con permisos mínimos
$pass = "StrongPassword123!";
$db = "usuarios";

try {
    $conn = new PDO("mysql:host=$host;dbname=$db;charset=utf8", $user, $pass);
    $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    
    // VALIDACIÓN Y SANITIZACIÓN
    $username = filter_input(INPUT_POST, 'username', FILTER_SANITIZE_STRING);
    $password = $_POST['password'];
    
    if (empty($username) || empty($password)) {
        throw new Exception("Todos los campos son requeridos");
    }
    
    // 🛡️ PREPARED STATEMENTS
    $query = "SELECT id, username, password_hash FROM users WHERE username = :username";
    $stmt = $conn->prepare($query);
    $stmt->bindParam(':username', $username, PDO::PARAM_STR);
    $stmt->execute();
    
    if ($stmt->rowCount() === 1) {
        $user = $stmt->fetch(PDO::FETCH_ASSOC);
        
        // 🛡️ VERIFICACIÓN CON password_verify()
        if (password_verify($password, $user['password_hash'])) {
            // Login exitoso
            session_start();
            $_SESSION['user_id'] = $user['id'];
            $_SESSION['username'] = $user['username'];
            
            header("Location: dashboard.php");
            exit();
        }
    }
    
    // Error genérico (no revelar información)
    throw new Exception("Credenciales incorrectas");
    
} catch (Exception $e) {
    error_log("Login error: " . $e->getMessage());
    echo "Error en el login. Intente nuevamente.";
}
?>
```

```html
<form method="POST" action="login.php">
    <input type="text" name="username" placeholder="Usuario" required>
    <input type="password" name="password" placeholder="Contraseña" required>
    <button type="submit">Ingresar</button>
</form>
```

Este otro ejemplo, por otro lado, tiene varias medidas de seguridad añadidas:
1. Evita la concatenación directa en la query
2. Hashea las contraseñas
3. Sanitización de entrada
4. Errores genéricos (no revelan información de la DB)
5. Principio de mínimo privilegio en el usuario de la DB
6. Token anti CSRF
7. Validación en el formulario


# Mitigación
- Criptografía: Hay que asegurar que toda la información, o al menos la sensible, esta correctamente protegida mediante métodos criptográficos para que en caso de sufrir una brecha, el atacante no tenga acceso a los datos en texto plano