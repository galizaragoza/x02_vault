# **Informe del proyecto final de seguridad en smartphones**
# Por Mario Hinojosa Freire
---

# Índice
- 
	- Metodología
	- APK deliberadamente vulnerable
		- Reconocimiento inicial
		- Análisis estático
			- Firma
			- Manifiesto de Android
			- Report MobSF
			- Código fuente
		- Análisis dinámico
	- APK de un repositorio no oficial
		- Reconocimiento inicial
		- Análisis estático
			- Firma
			- Manifiesto
	- APK oficial (Waze)
		- Reconocimiento inicial
		- Análisis estático
		- Análisis dinámico
			- ¿Porque no hay tráfico de Waze?
			- Posibles soluciones
	- Fuentes
- 

<div class="page-break" style="page-break-before: always;"></div>

# Metodología
Para esta actividad, se van a analizar 3 APKs, una deliberadamente vulnerable, una de repositorio no oficial, y por último una oficial (Waze). Para cada una de ellas de hará un reconocimiento con MobSF, un análisis estático con MobSF y Jadx y por último un análisis dinámico con Genymotion, Burpsuite y Objection.

El objetivo es hallar los permisos más críticos y elaborar un diagnóstico general del funcionamiento de la propia aplicación: Con que servidores comunica, que permisos tiene y como los usa, fallos de seguridad... Una vez acabado este reporte, debería estar claro el funcionamiento general de la aplicación, los entresijos de su funcionamiento y su comportamiento al estar activa.

Se va a ir tomando nota durante la investigación de las tecnologías utilizadas y las fuentes para citarlas después, aquí hay una tabla de las herramientas que a priori van a usarse en la actividad.

|            | Reconocimiento | Análisis estático | Análisis dinámico |
| ---------- | -------------- | ----------------- | ----------------- |
| MobSF      | ✅              | ✅                 | ✅                 |
| Jadx       |                | ✅                 | ✅                 |
| Genymotion |                |                   | ✅                 |
| Burpsuite  |                |                   | ✅                 |


<div class="page-break" style="page-break-before: always;"></div>

# APK deliberadamente vulnerable
Para la APK deliberadamente vulnerable, se ha escogido "[InsecureBankv2](https://github.com/dineshshetty/Android-InsecureBankv2/releases/tag/2.3.1)", un proyecto de seguridad de [dineshshetty](https://github.com/dineshshetty/Android-InsecureBankv2/commits?author=dineshshetty).

## Reconocimiento inicial
El archivo se sube a https://mobsf.live/ y se espera que finalice de ejecutarse en el análisis, una vez tenemos los resultados se empieza a trabajar. Como se puede apreciar en la imagen, la puntuación de la app es pésima.

![[mobSF_insbank.png]]

El análisis de MobSF reporta problemas en distintas áreas, firma, manifiesto, trackers y secretos hardcodeados, esto define el scope de la investigación a la aplicación al completo, ya que es vital verificar si los reportes de MobSF son falsos positivos o no y en el proceso analizar manualmente el resto del código fuente.

Para mayor precisión y ahora que tenemos una idea aproximada de lo que tenemos en frente, procederemos con el análisis estático, combinando el reporte de MobSF y Jadx

<div class="page-break" style="page-break-before: always;"></div>

## Análisis estático
### Firma
A primera vista resulta inseguro el certificado de la aplicación, que data de 2015 y está en la versión 1, que es vulnerable a Janus ([CVE-2017-13156](https://nvd.nist.gov/vuln/detail/CVE-2017-13156)). Que permite a un atacante modificar el código de una app sin alterar la firma, resultando en un vector de escalada de privilegios.

![[vulnerable_sign.png]]
![[vuln_sign_flaws.png]]

<div class="page-break" style="page-break-before: always;"></div>

### Manifiesto de Android
En el decompilador Jadx, analizamos `AndroidManifest.xml` (según la documentación de Android es una convención obligatoria con información esencial de la app para el SO).

![[vuln_manifest.png]]

Consultando la documentación de Android, desglosemos uno a uno los permisos.

- INTERNET 
	Permite abrir sockets de red
	
- **WRITE_EXTERNAL_STORAGE**
	Permite escribir al almacenamiento externo
	
- **SEND_SMS**
	Permite enviar SMS
	
- **USE_CREDENTIALS**
	Este permiso esta descontinuado desde Android 6.0 y no aparece en la documentación, por ende es complicado encontrar información sobre que hace exactamente, según un post de [stack overflow](https://stackoverflow.com/questions/32171204/android-studio-is-adding-android-permission-use-credentials-on-my-manifest), es para usar `AccountManager`, que es, según la documentación: "un registro centralizado de las cuentas online del usuario".
	
- **GET_ACCOUNTS**
	Permite acceso a la lista de cuentas del "Accounts Service"
	
- **READ_PROFILE**
	Parece estar descontinuado de nuevo ya que no aparece en la documentación, pero investigando se halla que su propósito es leer perfiles (IDs) de la base de datos de contactos.
	
- **READ_CONTACTS**
	Permite leer la información de los contactos
	
- **READ_PHONE_STATE**
	Permite lectura del estado del dispositivo (información de la red celular, estado de llamadas en curso y una lista de `PhoneAccounts` en el dispositivo)
		`PhoneAccount` presenta al usuario distintas opciones al realizar una llamada
		
- **READ_EXTERNAL_STORAGE**
	Permite leer del almacenamiento externo
	
- **READ_CALL_LOG**
	Permite leer el registro de llamadas
	
- ACCESS_NETWORK_STATE
	Permite acceso al estado de la red, también está descontinuado y borrado de la 
	documentación
	
- **ACCESS_COARSE_LOCATION**
	Permite acceder a la ubicación aproximada (a diferencia de `ACCESS_FINE_LOCATION`)

Los permisos en negrita son los marcados por MobSF como "dangerous". Se puede llegar a justificar lo relacionado a llamadas, contactos y SMS, por funcionalidades típicas de compartir recibos o incluso enviar Bizums, las credenciales podrían ser usadas para autenticación, pero la ubicación aproximada, a pesar de tener potenciales usos legítimos, resulta algo excesiva. Se reportan de nuevo como permisos habitualmente utilizados por malware en la sección del reporte de MobSF "Abused Permissions".

![[vuln_abuse_perms.png]]

El análisis también genera un reporte en el que se identifican 13 avisos de problemas con el manifiesto.

![[vuln_manif_report_1.png]]
![[vuln_manif_report_2.png]]

Las imágenes muestran todos los problemas del manifiesto detectados por MobSF, se contrastan uno por uno los avisos con el código decompilado en Jadx y se confirma la presencia de todas y cada una de las vulnerabilidades listadas en la tabla sobre este párrafo. Se volverá a referenciar más adelante en la fase de análisis del código tal que así: Aviso del manifiesto [1], aviso del manifiesto [2], siendo los números referencias al número de la tabla.

<div class="page-break" style="page-break-before: always;"></div>

### Resto del report de MobSF
Se reportan 3 trackers (los 3 de google), 

![[vuln_trackers.png]]

Así como 25 (potencialmente) secretos hardcodeados, 10 actividades, 2 recibidores y 1 proveedor, sin embargo, el análisis de estos avisos se va a llevar a cabo en la siguiente sección del análisis estático.

<div class="page-break" style="page-break-before: always;"></div>

### Código fuente
Importante: Los archivos analizados están cortados por una cuestión de legibilidad y ahorro de espacio, los archivos completos están disponibles en /insecure_src, los comentarios NO están generados por IA ni de forma automática, son observaciones manuales que indican el propósito del código o la infracción de OWASP que se comete en dicha línea. Algunos archivos que aparecen en el árbol de directorios más abajo no tienen un análisis porque se ha considerado que sus contenidos no eran lo suficientemente relevantes. 

Se cuenta un total de 14 archivos de código Java que componen el código fuente de la aplicación, más los archivos de Google ads que también entran en la carpeta `/com`, la estructura de la app es la siguiente:
```c
InsecureBankv2
	/Inputs
	/Código fuente
		/android.support
			/annotation
			/v4
			/v7
		/com
			/android.insecurebankv2 // Código fuente de la app
				BuildConfig.java
				ChangePassword.java
				CryptoClass.java
				DoLogin.java
				DoTransfer.java
				FilePrefActivity.java
				LoginActivity.java
				MyBroadCastReciever.java
				MyWebViewClient.java
				PostLogin.java
				R.java
				TrackUserContentProvider.java
				ViewStatement.java
				WrongLogin.java
			/google
				/ads // Anuncios de google
					/mediation
					AdRequest.java
					AdSize.java
				/android.gms // Servicios de Google Play
				...
	/Recursos
	APK signature
```

La carpeta que está dentro del scope y en la que más nos vamos a centrar es `/android.insecurebankv2`, ya que contiene el código propio de la app. En esta sección se analizará manualmente archivo por archivo, interpretando el código y en busca de vulnerabilidades, luego se verificará el diagnóstico elaborado a mano con modelos de lenguaje para asegurar la calidad del análisis.
<div class="page-break" style="page-break-before: always;"></div>


#### BuildConfig
Este archivo, como su propio nombre indica, establece la configuración del proyecto como tal. Define algunas variables públicas como el ID de la app, el tipo de build, la versión del código y demás. Se trata de un estándar de proyectos Android en Gradle.

El archivo define una `class`(objeto) de Java compuesta de 4 strings, un entero y un boolean, que definen ciertos aspectos, explicados en más detalle en los comentarios del código.

```java
package com.android.insecurebankv2;

public final class BuildConfig { // Define la clase BuildConfig
    public static final String APPLICATION_ID = "com.android.insecurebankv2";
    // Define el ID de la aplicación
    public sttic final String BUILD_TYPE = "debug";
    // Define el tipo de build
    public static final boolean DEBUG = Boolean.parseBoolean("true");
    // Da el valor TRUE a la variable DEBUG (declara el modo debug)
    public static final String FLAVOR = ""; 
    // Deja el string FLAVOR vacío (flavor es, según los docs, sirve para diferencias versiones de un producto que conviven en el mismo dispositivo, por ejemplo "demo" y "full").
    public static final int VERSION_CODE = 1;
    // Define el código de la versión de la build
    public static final String VERSION_NAME = "1.0";
    // Define el nombre de la versión de la build
}
```

<div class="page-break" style="page-break-before: always;"></div>

#### ChangePassword
En primer lugar, el archivo importa varias librerías y, en general, maneja la lógica del cambio de contraseña. Define el patrón posible de la contraseña con RegEx. Los caracteres mínimos (6) son insuficientes para una contraseña segura, tampoco previene contra contraseñas comunes ni enfrenta con blacklists(para prevenir cosas como "123456").

```java
    private static final String PASSWORD_PATTERN = "((?=.*\\d)(?=.*[a-z])(?=.*[A-Z])(?=.*[@#$%]).{6,20})"; //M4: Insecure Authentication
```

Después, invoca varias funciones que actualizan la contraseña en un servidor remoto, al ser una aplicación de pruebas no va a ningún servidor, pero cabe destacar que por defecto la función es insegura, ya que envía las nuevas credenciales por un canal no encriptado. Además, por alguna razón, leakea el username a los logs.

```java
    String serverip = "";
    String serverport = "";
    String protocol = "http://"; // M3: Insecure Communication
    ...
	System.out.println("newpassword=" + this.uname); // M2: Insecure Data Storage
```

El código hace una petición al endpoint "/changepassword", se asume que una vez propocionada una IP, el backend debe contener dicho endpoint con el resto de la lógica que sobreescriba los cambios en la base de datos. Se normaliza la contraseña y se pasa para ser actualizada.

```java
        public void postData(String valueIWantToSend) throws IllegalStateException, JSONException, BadPaddingException, NoSuchPaddingException, IllegalBlockSizeException, NoSuchAlgorithmException, IOException, InvalidKeyException, InvalidAlgorithmParameterException {
        
            HttpClient httpclient = new DefaultHttpClient();
            HttpPost httppost = new HttpPost(ChangePassword.this.protocol + ChangePassword.this.serverip + ":" + ChangePassword.this.serverport + "/changepassword");
            List<NameValuePair> nameValuePairs = new ArrayList<>(2);
            nameValuePairs.add(new BasicNameValuePair("username", ChangePassword.this.uname));
            nameValuePairs.add(new BasicNameValuePair("newpassword", ChangePassword.this.changePassword_text.getText().toString()));
            httppost.setEntity(new UrlEncodedFormEntity(nameValuePairs));
            ChangePassword.this.pattern = Pattern.compile(ChangePassword.PASSWORD_PATTERN);
            ChangePassword.this.matcher = ChangePassword.this.pattern.matcher(ChangePassword.this.changePassword_text.getText().toString());
```

Hay líneas más adelante que definen errores que se muestran según ciertas condiciones como contraseñas débiles.

```java
public void run() {
                    Toast.makeText(ChangePassword.this.getApplicationContext(), "Entered password is not complex enough.", 1).show();
                }
```

Y también contiene verificación por SMS (para 2FA), lógica que también es vulnerable, ya que manda por broadcast la nueva contraseña sin cifrar.

```  java
    public void broadcastChangepasswordSMS(String phoneNumber, String pass) {
        if (TextUtils.isEmpty(phoneNumber.toString().trim())) {
            System.out.println("Phone number Invalid.");
            return;
        }
        Intent smsIntent = new Intent();
        smsIntent.setAction("theBroadcast");
        smsIntent.putExtra("phonenumber", phoneNumber);
        smsIntent.putExtra("newpass", pass);
        sendBroadcast(smsIntent);
    }
```

<div class="page-break" style="page-break-before: always;"></div>

#### CryptoClass
A primera vista, parece ser código que gestiona la criptografía interna de la aplicación, salta a la vista nada más comenzar a leer que la clave, además de ser débil, esta expuesta en texto plano, además el vector de inicialización (ivBytes) es todo ceros.

```java
public class CryptoClass {
    String base64Text;
    byte[] cipherData;
    String cipherText;
    String plainText;
    String key = "This is the super secret key 123"; // Clave expuesta
    byte[] ivBytes = {0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0}; // M5: Insufficient Cryptography
```

Al menos, el algoritmo utilizado si es decente, aunque podría ser más robusto teniendo en cuenta que se trata de una app de banking.

```java
    public static byte[] aes256encrypt(byte[] ivBytes, byte[] keyBytes, byte[] textBytes) throws BadPaddingException, NoSuchPaddingException, IllegalBlockSizeException, NoSuchAlgorithmException, InvalidKeyException, UnsupportedEncodingException, InvalidAlgorithmParameterException {
        AlgorithmParameterSpec ivSpec = new IvParameterSpec(ivBytes);
        SecretKeySpec newKey = new SecretKeySpec(keyBytes, "AES"); // AES, algoritmo decente
        Cipher cipher = Cipher.getInstance("AES/CBC/PKCS5Padding");
        cipher.init(1, newKey, ivSpec);
        return cipher.doFinal(textBytes);
    }
```

<div class="page-break" style="page-break-before: always;"></div>

#### DoLogin
El archivo gestiona el inicio de sesión, toma nombre de usuario y contraseña y, al igual que ChangePassword, los pasa a través de HTTP a al servidor sin cifrar junto con algunas preferencias como recordar la contraseña/usuario.

```java
public class DoLogin extends Activity {
    public static final String MYPREFS = "mySharedPreferences";
    String password;
    BufferedReader reader;
    String rememberme_password;
    String rememberme_username;
    String result;
    SharedPreferences serverDetails;
    String superSecurePassword;
    String username;
    String responseString = null;
    String serverip = "";
    String serverport = "";
    String protocol = "http://"; // Método inseguro
```

Normaliza los campos de usuario y contraseña y lleva a cabo la petición al servidor

```java
protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_do_login);
        finish();
        this.serverDetails = PreferenceManager.getDefaultSharedPreferences(this);
        this.serverip = this.serverDetails.getString("serverip", null);
        this.serverport = this.serverDetails.getString("serverport", null);
        if (this.serverip != null && this.serverport != null) {
            Intent data = getIntent();
            this.username = data.getStringExtra("passed_username");
            this.password = data.getStringExtra("passed_password");
            new RequestTask().execute("username");
            return;
        }
```

Catastróficamente, el código leakea el endpoint de /devlogin, que seguramente se trate de un panel de administración, así como el nombre de usuario para acceder a dichos privilegios administrativos

```java
public void postData(String valueIWantToSend) throws IllegalStateException, BadPaddingException, JSONException, NoSuchPaddingException, IllegalBlockSizeException, NoSuchAlgorithmException, IOException, InvalidKeyException, InvalidAlgorithmParameterException {
            HttpResponse responseBody;
            HttpClient httpclient = new DefaultHttpClient();
            HttpPost httppost = new HttpPost(DoLogin.this.protocol + DoLogin.this.serverip + ":" + DoLogin.this.serverport + "/login");
            HttpPost httppost2 = new HttpPost(DoLogin.this.protocol + DoLogin.this.serverip + ":" + DoLogin.this.serverport + "/devlogin"); // Endpoint administrativo
            
...

if (DoLogin.this.username.equals("devadmin")) { // Username con privilegios
                httppost2.setEntity(new UrlEncodedFormEntity(nameValuePairs));
                responseBody = httpclient.execute(httppost2);
            } else {
                httppost.setEntity(new UrlEncodedFormEntity(nameValuePairs));
                responseBody = httpclient.execute(httppost);
            }
```

Por si fuera poco, se leakean a los logs el usuario y la contraseña

```java
if (DoLogin.this.result != null) {
                if (DoLogin.this.result.indexOf("Correct Credentials") != -1) {
                    Log.d("Successful Login:", ", account=" + DoLogin.this.username + ":" + DoLogin.this.password); // Leak de credenciales, crítico: M2: Insecure Data Storage
                    saveCreds(DoLogin.this.username, DoLogin.this.password);
                    trackUserLogins();
                    Intent pL = new Intent(DoLogin.this.getApplicationContext(), (Class<?>) PostLogin.class);
                    pL.putExtra("uname", DoLogin.this.username);
                    DoLogin.this.startActivity(pL);
                    return;
                }
```

Además, hay una función encargada de trackear al usuario que aparentemente no solicita consentimiento, suponiendo una violación grave de privacidad y leyes en varios países.

```java
        private void trackUserLogins() {
            DoLogin.this.runOnUiThread(new Runnable() {
                public void run() {
                    ContentValues values = new ContentValues();
                    values.put("name", DoLogin.this.username);
                    DoLogin.this.getContentResolver().insert(TrackUserContentProvider.CONTENT_URI, values); // Tracker sin popup o notificación solicitando consentimiento
                }
            });
        }
```

Las credenciales se almacenan cifradas(la contraseña) y encodeadas (el username), el nombre de usuario codificado en base64 no es crítico, sin embargo, al haberse expuesto la llave y el IV en el archivo anterior (CryptoClass), se trata de un fallo gravísimo, ya que cualquier contraseña se puede recuperar si se accede a la base de datos

```java
        private void saveCreds(String username, String password) throws BadPaddingException, NoSuchPaddingException, IllegalBlockSizeException, NoSuchAlgorithmException, InvalidKeyException, UnsupportedEncodingException, InvalidAlgorithmParameterException {
            SharedPreferences mySharedPreferences = DoLogin.this.getSharedPreferences("mySharedPreferences", 0);
            SharedPreferences.Editor editor = mySharedPreferences.edit();
            DoLogin.this.rememberme_username = username;
            DoLogin.this.rememberme_password = password;
            String base64Username = new String(Base64.encodeToString(DoLogin.this.rememberme_username.getBytes(), 4));
            CryptoClass crypt = new CryptoClass();
            DoLogin.this.superSecurePassword = crypt.aesEncryptedString(DoLogin.this.rememberme_password);
            editor.putString("EncryptedUsername", base64Username); // Codificado en base64
            editor.putString("superSecurePassword", DoLogin.this.superSecurePassword); // Cifrado, pero la clave y el IV se han leakeado en otras partes del código fuente
            editor.commit();
```

<div class="page-break" style="page-break-before: always;"></div>

#### DoTransfer
Este código se encarga de la gestión de las transferencias bancarias, en primer lugar, define una clase con algunos campos necesarios para la transferencia, como la cantidad, destinatario...

```java
public class DoTransfer extends Activity {
    public static final String MYPREFS2 = "mySharedPreferences";
    String acc1;
    String acc2;
    EditText amount;
    Button button1;
    EditText from;
    Button getAccounts;
    InputStream in;
    JSONObject jsonObject;
    String passNormalized;
    EditText phoneNumber;
    BufferedReader reader;
    HttpResponse responseBody;
    String result;
    SharedPreferences serverDetails;
    EditText to;
    Button transfer;
    String usernameBase64ByteString;
    String number = "5554"; // Posible número para SMS
    String serverip = "";
    String serverport = "";
    String protocol = "http://";
```

De nuevo, toda la operación sucede en HTTP, por lo que puede ser interpretada, dadas las vulnerabilidades previas de criptografía deficiente cualquier transferencia interceptada revela, además del origen, cantidad y destinatario, la contraseña, fácilmente desencritable para acceder a la cuenta del usuario.
Por si fuera poco, si se lograse inyectar de alguna manera ciertos campos en una transacción, podría ser posible transferir dinero sin necesidad de iniciar sesión. Esto podría llegar a hacerse si se estudia más a fondo el comportamiento de /dotransfer, si, por ejemplo, recibe la información con la que procesa las transacciones vía post, se podrían enviar solicitudes para hacer transferencias en nombre de otros usuarios dejando un rastro mínimo.

También falta validación en el lado del cliente, no se comprueban números negativos, no se establecen máximos de cantidad, y faltan mecanismos de autorización.

```java
        public String doInBackground(String... params) {
            HttpClient httpclient = new DefaultHttpClient();
            HttpPost httppost = new HttpPost(DoTransfer.this.protocol + DoTransfer.this.serverip + ":" + DoTransfer.this.serverport + "/dotransfer"); // Endpoint de transferencias
            SharedPreferences settings = DoTransfer.this.getSharedPreferences("mySharedPreferences", 0);
            String username = settings.getString("EncryptedUsername", null);
            byte[] usernameBase64Byte = Base64.decode(username, 0); // Leak del método de codificación del nombre de usuario
            try {
                DoTransfer.this.usernameBase64ByteString = new String(usernameBase64Byte, "UTF-8");
            } catch (UnsupportedEncodingException e) {
                e.printStackTrace();
            }
            String password = settings.getString("superSecurePassword", null);
            try {
                DoTransfer.this.passNormalized = DoTransfer.this.getNormalizedPassword(password);
            } catch (UnsupportedEncodingException | InvalidAlgorithmParameterException | InvalidKeyException | NoSuchAlgorithmException | BadPaddingException | IllegalBlockSizeException | NoSuchPaddingException e1) {
                e1.printStackTrace();
            }
            List<NameValuePair> nameValuePairs = new ArrayList<>(5);
            nameValuePairs.add(new BasicNameValuePair("username", DoTransfer.this.usernameBase64ByteString));
            nameValuePairs.add(new BasicNameValuePair("password", DoTransfer.this.passNormalized));
  
            DoTransfer.this.from = (EditText) DoTransfer.this.findViewById(R.id.editText_from);
            DoTransfer.this.to = (EditText) DoTransfer.this.findViewById(R.id.editText_to);
            DoTransfer.this.amount = (EditText) DoTransfer.this.findViewById(R.id.editText_amount);
            nameValuePairs.add(new BasicNameValuePair("from_acc", DoTransfer.this.from.getText().toString())); // Variable from_acc potencialmente spoofeable
            nameValuePairs.add(new BasicNameValuePair("to_acc", DoTransfer.this.to.getText().toString())); // Variable to_acc potencialmente spoofeable
            nameValuePairs.add(new BasicNameValuePair("amount", DoTransfer.this.amount.getText().toString())); // Falta flagrante de validación-->M7: Client Code Quality
```

Se leakean de nuevo el destinatario, remitente y cantidad de la transferencia en a los logs

```java
                public void run() throws IOException {
                    RequestDoTransferTask.this.AsyncHttpTransferPost("result");
                    if (DoTransfer.this.result != null) {
                        if (DoTransfer.this.result.indexOf("Success") != -1) {
                            Toast.makeText(DoTransfer.this.getApplicationContext(), "Transfer Successful", 1).show();
                            try {
                                DoTransfer.this.jsonObject = new JSONObject(DoTransfer.this.result);
                                DoTransfer.this.acc1 = DoTransfer.this.jsonObject.getString("from");
                                DoTransfer.this.acc2 = DoTransfer.this.jsonObject.getString("to");
                                System.out.println("Message:" + DoTransfer.this.jsonObject.getString("message") + " From:" + DoTransfer.this.from.getText().toString() + " To:" + DoTransfer.this.to.getText().toString() + " Amount:" + DoTransfer.this.amount.getText().toString()); // Leak de información sensible
```

<div class="page-break" style="page-break-before: always;"></div>

#### FilePrefActivity
El código de este archivo monitoriza las preferencias del usuario, la clase definida al inicio es pequeña comparada con otros archivos analizados, definiendo variables de preferencias.

```java
public class FilePrefActivity extends Activity {
    static EditText edit_serverip;
    static EditText edit_serverport;
    SharedPreferences.Editor editor;
    SharedPreferences preferences;
    Button submitPref_buttonz;
```

<div class="page-break" style="page-break-before: always;"></div>

#### LoginActivity
Este archivo contiene más funcionalidad de autenticación, incluye una función que hace alusión a la creación de usuarios pero al parecer esta esta en desarrollo aún, en primer lugar, define la clase LoginActivity.

```java
public class LoginActivity extends Activity {
    public static final String MYPREFS = "mySharedPreferences";
    EditText Password_Text;
    EditText Username_Text;
    Button createuser_buttons;
    Button fillData_button;
    Button login_buttons;
    String usernameBase64ByteString;
    
    ...
    
        protected void createUser() {
        Toast.makeText(this, "Create User functionality is still Work-In-Progress!!", 1).show();
    } // Creación de usuario en proceso de desarrollo
```

Para evitar ser redundante con los bloques de código no se mostrarán en este archivo, ya que los patrones son muy similares, pero contiene las mismas vulnerabilidades de criptografía débil mencionadas en análisis previos.

<div class="page-break" style="page-break-before: always;"></div>

#### MyBroadCastReciever
Este archivo contiene una única función, flagrantemente vulnerable también, además de las vulnerabilidades ya mencionadas de criptografía insuficiente, los desarrolladores incurren de nuevo en el uso de canales inseguros, enviando información crítica a través de estos. En este caso, la contraseña recién cambiada en texto plano por SMS y leakeandola también a los logs.

```java
    public void onReceive(Context context, Intent intent) {
        String phn = intent.getStringExtra("phonenumber");
        String newpass = intent.getStringExtra("newpass");
        if (phn != null) {
            try {
                SharedPreferences settings = context.getSharedPreferences("mySharedPreferences", 1);
                String username = settings.getString("EncryptedUsername", null);
                byte[] usernameBase64Byte = Base64.decode(username, 0);
                this.usernameBase64ByteString = new String(usernameBase64Byte, "UTF-8");
                String password = settings.getString("superSecurePassword", null);
                CryptoClass crypt = new CryptoClass();
                String decryptedPassword = crypt.aesDeccryptedString(password);
                String textPhoneno = phn.toString();
                String textMessage = "Updated Password from: " + decryptedPassword + " to: " + newpass; // Credenciales leakeadas por SMS
                SmsManager smsManager = SmsManager.getDefault();
                System.out.println("For the changepassword - phonenumber: " + textPhoneno + " password is: " + textMessage);  // Credenciales leakeadas a los logs
                smsManager.sendTextMessage(textPhoneno, null, textMessage, null, null);
                return;
            } catch (Exception e) {
                e.printStackTrace();
                return;
            }
        }
        System.out.println("Phone number is null");
    }
}
```

<div class="page-break" style="page-break-before: always;"></div>

#### PostLogin
Este archivo hace algunas comprobaciones sobre el estado del dispositivo, por ejemplo, revisa si se encuentra o no rooteado. O si existe o no un superuser.

```java
    void showRootStatus() {
        boolean isrooted = doesSuperuserApkExist("/system/app/Superuser.apk") || doesSUexist();
        if (isrooted) {
            this.root_status.setText("Rooted Device!!");
        } else {
            this.root_status.setText("Device not Rooted!!");
        }
    }
    
        private boolean doesSUexist() {
        Process process = null;
        try {
            process = Runtime.getRuntime().exec(new String[]{"/system/xbin/which", "su"});
            BufferedReader in = new BufferedReader(new InputStreamReader(process.getInputStream()));
            if (in.readLine() == null) {
                if (process != null) {
                    process.destroy();
                }
                return false;
            }
            if (process == null) {
                return true;
            }
            process.destroy();
            return true;
        } catch (Throwable th) {
            if (process != null) {
                process.destroy();
            }
            throw th;
        }
    }
```

<div class="page-break" style="page-break-before: always;"></div>

#### R
Este archivo es generado automáticamente por Java que referencian una serie de recursos usados en un proyecto Android, desde funciones hasta elementos de la UI.

#### TrackUserContentProvider
Como el propio nombre indica, aquí encontramos lógica para manejar el tracking (no autorizado) a los usuarios, el archivo comienza con la definición de una clase que define variables como la base de datos a la que enviar la información.

```java
public class TrackUserContentProvider extends ContentProvider {
    static final String CREATE_DB_TABLE = " CREATE TABLE names (id INTEGER PRIMARY KEY AUTOINCREMENT,  name TEXT NOT NULL);";
    static final String DATABASE_NAME = "mydb";
    static final int DATABASE_VERSION = 1;
    static final String PROVIDER_NAME = "com.android.insecurebankv2.TrackUserContentProvider";
    static final String TABLE_NAME = "names";
    static final String name = "name";
    static final int uriCode = 1;
    private static HashMap<String, String> values;
    private SQLiteDatabase db;
    static final String URL = "content://com.android.insecurebankv2.TrackUserContentProvider/trackerusers";
    static final Uri CONTENT_URI = Uri.parse(URL);
    static final UriMatcher uriMatcher = new UriMatcher(-1);
    static {
        uriMatcher.addURI(PROVIDER_NAME, "trackerusers", 1);
        uriMatcher.addURI(PROVIDER_NAME, "trackerusers/*", 1);
    }
```

El código es potencialmente vulnerable a inyecciones SQL

```java
    private static class DatabaseHelper extends SQLiteOpenHelper {
        DatabaseHelper(Context context) {
            super(context, TrackUserContentProvider.DATABASE_NAME, (SQLiteDatabase.CursorFactory) null, 1); // Falta de sanitización
        }
```

Falta consentimiento del usuario y opción de opt-out, suponiendo delito en varias jurisdicciones.

<div class="page-break" style="page-break-before: always;"></div>

#### ViewStatement
Se identifican en este código algunas potenciales vulnerabilidades, en esta función, por ejemplo, "uname" viene sin sanitizar, lo que supone la posibilidad de atacar la aplicación mediante inyecciones a través de esa variable, para XSS o Path Traversal.

```java
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_view_statement);
        Intent intent = getIntent();
        this.uname = intent.getStringExtra("uname");
        String FILENAME = "Statements_" + this.uname + ".html";
        File fileToCheck = new File(Environment.getExternalStorageDirectory(), FILENAME);
        System.out.println(fileToCheck.toString());
        if (fileToCheck.exists()) {
            WebView mWebView = (WebView) findViewById(R.id.webView1);
            mWebView.loadUrl("file://" + Environment.getExternalStorageDirectory() + "/Statements_" + this.uname + ".html");
            mWebView.getSettings().setJavaScriptEnabled(true);
            mWebView.getSettings().setSaveFormData(true);
            mWebView.getSettings().setBuiltInZoomControls(true);
            mWebView.setWebViewClient(new MyWebViewClient());
            WebChromeClient cClient = new WebChromeClient();
            mWebView.setWebChromeClient(cClient);
            return;
        }
```

<div class="page-break" style="page-break-before: always;"></div>

## Análisis dinámico
Para esta fase del reconicimiento, se instala la apk en cuestión en una máquina virtual con Genymotion, y se configura Burp Suite para interceptar al tráfico y analizarlo.

Se levanta también, el servidor de la máquina para poder analizar el tráfico correctamente

Dicho esto, procedemos con el análisis. Justo al entrar, se nos recibe con un formulario sencillo de login

![[vuln_app_login.png]]

Como ya sabíamos, el transporte de las credenciales es flagrantemente inseguro y viaja sin cifrado, listo para ser interceptado.

```c
POST /login HTTP/1.1
Content-Length: 25
Content-Type: application/x-www-form-urlencoded
Host: 192.168.1.157:8888
Connection: keep-alive
User-Agent: Apache-HttpClient/UNAVAILABLE (java 1.4)

username=123&password=123 // Tráfico en texto plano
```

Las instrucciones del repositorio proporcionan varias cuentas para testear la aplicación, entre ellas: `dinesh/Dinesh@123$`.

En primer lugar, salta a la vista un aviso de la aplicación que indica que el dispositivo está rooteado, lo cual es cierto, sin embargo, no se implementa ninguna medida extra de seguridad más allá del aviso.

![[vuln_root_warning.png]]

Como ya se sospechaba, la generación de los IDs de cuenta no son para nada aleatorios, lo que podría llevar a adivinar o predecir cuentas con mucha facilidad.

![[vuln_trans.png]]

De nuevo, las credenciales viajan totalmente visibles por HTTP, se prueba a modificar la cantidad de la transferencia en el proxy Burpsuite de 6000 a 60000

```c
POST /dotransfer HTTP/1.1
Content-Length: 88
Content-Type: application/x-www-form-urlencoded
Host: 192.168.1.157:8888
Connection: keep-alive
User-Agent: Apache-HttpClient/UNAVAILABLE (java 1.4)

username=dinesh&password=Dinesh%40123%24&from_acc=888888888&to_acc=666666666&amount=60000 // Credenciales
```

Al modificar la transacción interceptada, se acepta sin problemas, se prueba también con cifras ridículamente altas (1 billón) y la transferencia se admite.

![[transfer_successful.png]]
![[vuln_statement.png]]

En cuanto a la funcionalidad de cambiar contraseña, si tiene comprobaciones de complejidad, sin embargo, además de poder ser interceptada y manipulada, no pide confirmación de ningún tipo

```c
POST /changepassword HTTP/1.1
Content-Length: 54
Content-Type: application/x-www-form-urlencoded
Host: 192.168.1.157:8888
Connection: keep-alive
User-Agent: Apache-HttpClient/UNAVAILABLE (java 1.4)

username=dinesh&newpassword=AtriumSmart123%21%21%23%23
```

![[vuln_pass_change.png]]

<div class="page-break" style="page-break-before: always;"></div>

# APK de un repositorio no oficial
Para esta fase del informe se ha escogido la aplicación "Net Master", distribuida en APKPure por [Hi Security Lab (Antivirus, AppLock & VPN Free)](https://apkpure.com/developer/Hi%20Security%20Lab%20\(Antivirus%2C%20AppLock%20%26%20VPN%20Free\) "Get more from Hi Security Lab (Antivirus, AppLock & VPN Free)"), una empresa investigada por distribuir malware en Android. La aplicación fue actualizada por última vez en 2020 y aparece en varias listas de aplicaciones retiradas de la Play Store por contener malware. 


## Reconocimiento inicial

![[unof_score 1.png]]

La APK puntúa 47/100 en el análisis de MobSF, el cuál alerta de 12 trackers detectados. Por otro lado, pasando el hash por VirusTotal, se puede observar que dos proveedores de antivirus detectan el archivo como malware. 

![[virtustotal_unof.png]]

## Análisis estático

### Firma
Analizando la firma del binario, vemos que la versión es v2, lo cual significa que la app es vulnerable a Janus. La firma se emite en 2016 y es válida por 1 siglo, en cuanto al *issuer*, el valor es algo así como un comodín (`Issuer: CN=tcl`).

```java
Binary is signed
v1 signature: True
v2 signature: True
v3 signature: False
v4 signature: False
X.509 Subject: CN=tcl
Signature Algorithm: rsassa_pkcs1v15
Valid From: 2016-06-17 09:31:16+00:00
Valid To: 2116-05-24 09:31:16+00:00
Issuer: CN=tcl
Serial Number: 0xdeb21ad
Hash Algorithm: sha256
md5: 2f6c2f925d55102c38185925761183a9
sha1: 17681f3d1c51dea9de74d1ec2ab77c84eb06e5bd
sha256: ca18f4d5cd77c9d931dc183d2b30755c11efe0fc13dcd1befc0bb88159b57191
sha512: cfce0d1af3a04ee0050f85b5bfdf5018e0ddc0be6420d60c401e91a27313ba3547dcbf79dffe514952d2781f5bb21f0f826d2ea2d11e34d6efc57598238222af
PublicKey Algorithm: rsa
Bit Size: 2048
Fingerprint: 4ceba9a2b9f624b715e99463419068bb5a90aaf7ba8a0340210279356fba938c
Found 1 unique certificates
```

<div class="page-break" style="page-break-before: always;"></div>

### Manifiesto
En el manifiesto se pueden analizar los permisos que se conceden al binario, de la lista completa bajo este párrado se elabora una tabla comentando y evaluando el riesgo de los más críticos. Los permisos que quedan fuera de la lista son los evaluados por la documentación de Android y el propio análisis de MobSF como inocuos/normales.

```java
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE"/>
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
    <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>
    <uses-permission android:name="android.permission.GET_TASKS"/>
    <uses-permission android:name="android.permission.KILL_BACKGROUND_PROCESSES"/>
    <uses-permission android:name="android.permission.READ_PHONE_STATE"/>
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
    <uses-permission android:name="android.permission.VIBRATE"/>
  
    <uses-permission android:name="android.permission.BLUETOOTH"/>
    <uses-permission android:name="android.permission.BLUETOOTH_ADMIN"/>
    <uses-permission android:name="android.permission.FLASHLIGHT"/>
    <uses-permission android:name="android.permission.FOREGROUND_SERVICE"/>
    <uses-feature
        android:name="android.hardware.bluetooth"
        android:required="false"/>
    <uses-feature
        android:name="android.hardware.camera"
        android:required="false"/>
    <uses-feature
        android:name="android.hardware.camera.autofocus"
        android:required="false"/>
    <uses-feature
        android:name="android.hardware.camera.flash"
        android:required="false"/>
    <permission
        android:name="android.permission.INTERACT_ACROSS_USERS"
        android:protectionLevel="signature"/>
    <uses-permission android:name="android.permission.PACKAGE_USAGE_STATS"/>
    <uses-permission android:name="com.android.launcher.permission.INSTALL_SHORTCUT"/>
    <uses-permission android:name="com.android.launcher.permission.UNINSTALL_SHORTCUT"/>
    <uses-permission android:name="com.android.launcher.permission.READ_SETTINGS"/>
    <uses-permission android:name="com.android.launcher2.permission.READ_SETTINGS"/>
    <uses-permission android:name="com.android.launcher3.permission.READ_SETTINGS"/>
    <uses-permission android:name="com.google.android.launcher.permission.READ_SETTINGS"/>
    <uses-permission android:name="com.tct.launcher.permission.READ_SETTINGS"/>
    <uses-permission android:name="org.adw.launcher.permission.READ_SETTINGS"/>
    <uses-permission android:name="com.htc.launcher.permission.READ_SETTINGS"/>
    <uses-permission android:name="com.qihoo360.launcher.permission.READ_SETTINGS"/>
    <uses-permission android:name="com.lge.launcher.permission.READ_SETTINGS"/>
    <uses-permission android:name="net.qihoo.launcher.permission.READ_SETTINGS"/>
    <uses-permission android:name="org.adwfreak.launcher.permission.READ_SETTINGS"/>
    <uses-permission android:name="org.adw.launcher_donut.permission.READ_SETTINGS"/>
    <uses-permission android:name="com.huawei.launcher3.permission.READ_SETTINGS"/>
    <uses-permission android:name="com.fede.launcher.permission.READ_SETTINGS"/>
    <uses-permission android:name="com.sec.android.app.twlauncher.settings.READ_SETTINGS"/>
    <uses-permission android:name="com.anddoes.launcher.permission.READ_SETTINGS"/>
    <uses-permission android:name="com.tencent.qqlauncher.permission.READ_SETTINGS"/>
    <uses-permission android:name="com.huawei.launcher2.permission.READ_SETTINGS"/>
    <uses-permission android:name="com.android.mylauncher.permission.READ_SETTINGS"/>
    <uses-permission android:name="com.ebproductions.android.launcher.permission.READ_SETTINGS"/>
    <uses-permission android:name="com.oppo.launcher.permission.READ_SETTINGS"/>
    <uses-permission android:name="com.huawei.android.launcher.permission.READ_SETTINGS"/>
    <uses-permission android:name="telecom.mdesk.permission.READ_SETTINGS"/>
    <uses-permission android:name="net.oneplus.launcher.permission.READ_SETTINGS"/>
    <uses-permission android:name="com.blackberry.blackberrylauncher.permission.READ_SETTINGS"/>
    <uses-permission android:name="com.google.android.apps.nexuslauncher.permission.READ_SETTINGS"/>
    <uses-permission android:name="com.google.android.apps.nexuslauncher.permission.WRITE_SETTINGS"/>
    <uses-permission android:name="android.permission.EXPAND_STATUS_BAR"/>
    <uses-permission android:name="android.permission.WRITE_SETTINGS"/>
    <uses-permission android:name="com.mediatek.permission.CTA_ENABLE_WIFI"/>
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.CAMERA"/>
    <uses-permission android:name="android.permission.CHANGE_NETWORK_STATE"/>
    <uses-permission android:name="android.permission.GET_INTENT_SENDER_INTENT"/>
    <uses-permission android:name="android.permission.REQUEST_DELETE_PACKAGES"/>
    <uses-permission android:name="android.permission.REQUEST_INSTALL_PACKAGES"/>
    <uses-permission android:name="android.permission.WAKE_LOCK"/>
    <uses-permission android:name="com.google.android.finsky.permission.BIND_GET_INSTALL_REFERRER_SERVICE"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE"/>
```


| **Permiso**                                      | **Función**                                                                                                       | **Comentario**                                                                                                                                                              |
| ------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ACCESS_COARSE_LOCATION                           | Permite acceso a la ubicación aproximada (mediante red celular) del dispositivo                                   | Al incluir presuntamente una VPN podría llegar a estar justificado                                                                                                          |
| ACCESS_FINE_LOCATION                             | Permite acceso a la ubicación precisa (GPS) del dispositivo                                                       | Resulta excesivo para una aplicación de estas características                                                                                                               |
| CAMERA                                           | Permite acceso a la cámara (tomar fotos, vídeos...)                                                               | Totalmente innecesario, con la posible excepción de hacer fotos a un documento o algo similar para verificar identidad                                                      |
| GET_TASKS                                        | Permite a la app acceder a información sobre otros procesos activos o recientes                                   | Podría tener sentido para la monitorización de red que incluye la app                                                                                                       |
| READ_PHONE_STATE                                 | Permite a la app acceder a información del dispositivo como nº de serie, si está o no en llamada y con quien, etc | Excesivo e innecesario                                                                                                                                                      |
| REQUEST_INSTALL_PACKAGES                         | Permite a la aplicación instalar paquetes (solicitando confirmación de usuario)                                   | En principio resulta excesivo, no es crítico por la confirmación requerida, pero a no ser que la app necesite paquetes externos para funcionar correctamente es innecesario |
| SYSTEM_ALERT_WINDOW                              | Permite mostrar alertas de sistema                                                                                | Tiene usos potenciales maliciosos pero puede ser un permiso legítimo en una app de estas características                                                                    |
| WRITE_EXTERNAL_STORAGE                           | Permite escribir al almacenamiento externo                                                                        | Excesivo y peligroso                                                                                                                                                        |
| WRITE_SETTINGS                                   | Permite modificar los ajustes del dispositivo                                                                     | Podría estar justificado si se requiere modificar ajustes para conexión VPN o monitorizar el rendimiento de la red                                                          |
| apps.nexuslauncher.<br>permission.WRITE_SETTINGS | Permite modificar los ajustes de la app nexuslauncher                                                             | Podría estar justificado si se requiere modificar ajustes para conexión VPN o monitorizar el rendimiento de la red                                                          |

Además, MobSF alerta sobre otros permisos habitualmente encontrados en malware, sin embargo, teniendo en cuenta los servicios que la aplicación ofrece, son permisos razonables.

| **Permiso**            | **Función**                                                                                         |
| ---------------------- | --------------------------------------------------------------------------------------------------- |
| ACCESS_WIFI_STATE      | Permite acceso a información sobre redes WiFi                                                       |
| ACCESS_NETWORK_STATE   | Permite acceso a información sobre la red                                                           |
| INTERNET               | Permite a la app abrir sockets de red                                                               |
| RECEIVE_BOOT_COMPLETED | Permite a la app recibir un broadcast interno que se emite cuando el dispositivo termina de bootear |
| WAKE_LOCK              | Permite a la app impedir a la pantalla de apagarse o al procesador de "dormirse"                    |

### Código fuente
En base a las alertas que da el análisis de MobSF se analiza el código fuente de la aplicación vía Jadx para mayor rigor con el propósito de contrastar los hallazgos de vulnerabilidades de MobSF.

#### M2: Insecure Data Storage
En los archivos `com/appsflyer/AppsFlyerLibCore.java` y `com/baron/MPSharedPreferences/b.java` se invoca una API interna de Android (`SharedPreferences`) que es modificable por cualquier usuario y se utiliza para almacenar información, incurriendo en M2 de OWASP Móvil. No se analiza el archivo completo porque contiene miles de líneas, pero aquí se puede ver dicha llamada y como se añade información a dicha DB.

```java
...

import android.content.SharedPreferences;

...

    public static void m41(Context context, String str, long j) {
        SharedPreferences.Editor editorEdit = context.getApplicationContext().getSharedPreferences("appsflyer-data", 0).edit();
        editorEdit.putLong(str, j);
        editorEdit.apply();
    }
    
...

```

<div class="page-break" style="page-break-before: always;"></div>

#### M3: Insecure Communication
MobSF flaguea el archivo  `com/mopub/network/CustomSSLSocketFactory.java` por utilizar comunicaciones inseguras, en concreto, por confiar en todos los certificados o en certificados autofirmados. Investigando el código en más profundidad con asistencia de LLM, saltan a la vista 3 graves problemas.
- SNI se deshabilita en versiones antiguas
- Se habilitan protocolos SSL/TLS vulnerables/descontinuados
- La verificación del hostname es insegura

```java
public class CustomSSLSocketFactory extends SSLSocketFactory {

    /* renamed from: a, reason: collision with root package name */
    private SSLSocketFactory f14640a;

    private CustomSSLSocketFactory() {
    }

    public static CustomSSLSocketFactory getDefault(int i) {
        CustomSSLSocketFactory customSSLSocketFactory = new CustomSSLSocketFactory();
        customSSLSocketFactory.f14640a = SSLCertificateSocketFactory.getDefault(i, null);
        return customSSLSocketFactory;
    }

    @Override // javax.net.SocketFactory
    public Socket createSocket() throws IOException {
        if (this.f14640a == null) {
            throw new SocketException("SSLSocketFactory was null. Unable to create socket.");
        }
        Socket socketCreateSocket = this.f14640a.createSocket();
        a(socketCreateSocket);
        return socketCreateSocket;
    }

    @Override // javax.net.SocketFactory
    public Socket createSocket(String str, int i) throws IOException {
        if (this.f14640a == null) {
            throw new SocketException("SSLSocketFactory was null. Unable to create socket.");
        }
        Socket socketCreateSocket = this.f14640a.createSocket(str, i);
        a(socketCreateSocket);
        return socketCreateSocket;
    }

    @Override // javax.net.SocketFactory
    public Socket createSocket(String str, int i, InetAddress inetAddress, int i2) throws IOException {
        if (this.f14640a == null) {
            throw new SocketException("SSLSocketFactory was null. Unable to create socket.");
        }
        Socket socketCreateSocket = this.f14640a.createSocket(str, i, inetAddress, i2);
        a(socketCreateSocket);
        return socketCreateSocket;
    }

    @Override // javax.net.SocketFactory
    public Socket createSocket(InetAddress inetAddress, int i) throws IOException {
        if (this.f14640a == null) {
            throw new SocketException("SSLSocketFactory was null. Unable to create socket.");
        }
        Socket socketCreateSocket = this.f14640a.createSocket(inetAddress, i);
        a(socketCreateSocket);
        return socketCreateSocket;
    }

    @Override // javax.net.SocketFactory
    public Socket createSocket(InetAddress inetAddress, int i, InetAddress inetAddress2, int i2) throws IOException {
        if (this.f14640a == null) {
            throw new SocketException("SSLSocketFactory was null. Unable to create socket.");
        }
        Socket socketCreateSocket = this.f14640a.createSocket(inetAddress, i, inetAddress2, i2);
        a(socketCreateSocket);
        return socketCreateSocket;
    }

    @Override // javax.net.ssl.SSLSocketFactory
    public String[] getDefaultCipherSuites() {
        if (this.f14640a == null) {
            return new String[0];
        }
        return this.f14640a.getDefaultCipherSuites();
    }

    @Override // javax.net.ssl.SSLSocketFactory
    public String[] getSupportedCipherSuites() {
        if (this.f14640a == null) {
            return new String[0];
        }
        return this.f14640a.getSupportedCipherSuites();
    }

    @Override // javax.net.ssl.SSLSocketFactory
    public Socket createSocket(Socket socket, String str, int i, boolean z) throws IOException {
        if (this.f14640a == null) {
            throw new SocketException("SSLSocketFactory was null. Unable to create socket.");
        }
        if (Build.VERSION.SDK_INT < 23) { // Deshabilita SNI en versiones antiguas
            if (z && socket != null) {
                socket.close();
            }
            Socket socketCreateSocket = this.f14640a.createSocket(InetAddressUtils.getInetAddressByName(str), i);
            a(socketCreateSocket);
            a(socketCreateSocket, str);
            return socketCreateSocket;
        }
        Socket socketCreateSocket2 = this.f14640a.createSocket(socket, str, i, z);
        a(socketCreateSocket2);
        return socketCreateSocket2;
    }

    private void a(Socket socket, String str) throws IOException {
        Preconditions.checkNotNull(socket);
        if (this.f14640a == null) {
            throw new SocketException("SSLSocketFactory was null. Unable to create socket.");
        }
        if (socket instanceof SSLSocket) {
            SSLSocket sSLSocket = (SSLSocket) socket;
            a((SSLCertificateSocketFactory) this.f14640a, sSLSocket, str);
            a(sSLSocket, str);
        }
    }

    @VisibleForTesting
    static void a(SSLCertificateSocketFactory sSLCertificateSocketFactory, SSLSocket sSLSocket, String str) {
        Preconditions.checkNotNull(sSLCertificateSocketFactory);
        Preconditions.checkNotNull(sSLSocket);
        if (Build.VERSION.SDK_INT >= 17) {
            sSLCertificateSocketFactory.setHostname(sSLSocket, str);
            return;
        }
        try {
            new Reflection.MethodBuilder(sSLSocket, "setHostname").addParam((Class<Class>) String.class, (Class) str).execute();
        } catch (Exception unused) {
            MoPubLog.d("Unable to call setHostname() on the socket");
        }
    }

    @VisibleForTesting
    static void a(SSLSocket sSLSocket, String str) throws IOException {
        Preconditions.checkNotNull(sSLSocket);
        sSLSocket.startHandshake(); // Verificación ocurre después del handshake
        if (!HttpsURLConnection.getDefaultHostnameVerifier().verify(str, sSLSocket.getSession())) {
            throw new SSLHandshakeException("Server Name Identification failed.");
        }
    }

    private void a(Socket socket) {
        if (socket instanceof SSLSocket) {
            SSLSocket sSLSocket = (SSLSocket) socket;
            sSLSocket.setEnabledProtocols(sSLSocket.getSupportedProtocols()); // Uso de protocolos vulnerables
        }
    }
}
```

<div class="page-break" style="page-break-before: always;"></div>

#### M1: Improper Platform Usage
El debugging de WebView en remoto está habilitado en el archivo `com/inmobi/rendering/RenderView.java`.

```java
...

import android.webkit.WebView;
import android.webkit.WebViewClient;
  
...

public final class RenderView extends WebView implements AdContainer, b {

...

        this.ah = new WebViewClient() { // from class: com.inmobi.rendering.RenderView.4
            @Override // android.webkit.WebViewClient
            public final boolean shouldOverrideUrlLoading(WebView webView, WebResourceRequest webResourceRequest) {
                String unused = RenderView.x;
```

<div class="page-break" style="page-break-before: always;"></div>

#### Logeo de información
En una gran lista de archivos, la aplicación contiene lógica que escribe a los logs del dispositivo, esto supone un fallo grave si entre esos logs hay información sensible.

Por ejemplo, en este archivo (`com/inmobi/commons/core/utilities/Logger.java`) vemos como se loguea información de errores y debug.

```java
  public static void a(InternalLogLevel internalLogLevel, String str, String str2) {
        if (internalLogLevel.ordinal() <= f12626a.ordinal()) {
            switch (internalLogLevel) {
                case ERROR:
                    Log.e("[InMobi]", str2);
                    break;
                case DEBUG:
                    Log.d("[InMobi]", str2);
                    break;
                case INTERNAL:
                    if (str2.length() <= 4000) {
                        Log.d(str, str2);
                        break;
                    } else {
                        while (str2.length() > 4000) {
                            Log.d(str, str2.substring(0, 4000));
                            str2 = str2.substring(4000);
                        }
                        Log.d(str, str2);
                        break;
                    }
            }
        }
    }
```

En este otro (`com/mopub/common/LocationService.java`), se filtra información sobre el proveedor de la localización:

```java
    static Location a(Context context, ValidLocationProvider validLocationProvider) {
        Preconditions.checkNotNull(context);
        Preconditions.checkNotNull(validLocationProvider);
        if (!MoPub.canCollectPersonalInformation() || !validLocationProvider.a(context)) {
            return null;
        }
        try {
            return ((LocationManager) context.getSystemService(GooglePlayServicesInterstitial.LOCATION_KEY)).getLastKnownLocation(validLocationProvider.toString());
        } catch (IllegalArgumentException unused) {
            MoPubLog.d("Failed to retrieve location: device has no " + validLocationProvider.toString() + " location provider.");
            return null;
        } catch (NullPointerException unused2) {
            MoPubLog.d("Failed to retrieve location: device has no " + validLocationProvider.toString() + " location provider.");
            return null;
        } catch (SecurityException unused3) {
            MoPubLog.d("Failed to retrieve location from " + validLocationProvider.toString() + " provider: access appears to be disabled.");
            return null;
        }
    }
```

Y la lista de archivos que escriben a los logs continúa con 152 archivos, muchos de ellos solo escriben información inocua, probablemente código residual de la fase de desarrollo para debugging. Otros tantos si filtran información que podría considerarse crítica.

<div class="page-break" style="page-break-before: always;"></div>

#### M7: Client Code Quality (SQLi)
El código es en varios archivos vulnerable a inyecciones SQL, pasa queries no sanitizadas al backend en varias ocasiones.

```java
/* com/ehawk/speedtest/netmaster/sqlite/c.java */

    public void onCreate(SQLiteDatabase sQLiteDatabase) throws SQLException {
        com.ehawk.speedtest.netmaster.c.a.b("db", "DatabaseHelper  onCreate");
        sQLiteDatabase.execSQL(b.a.a().toString());
        sQLiteDatabase.execSQL(b.a.a().toString());
        sQLiteDatabase.execSQL(b.a.a().toString());
        sQLiteDatabase.execSQL(b.a.a().toString());
    }
  
  
    public void onOpen(SQLiteDatabase sQLiteDatabase) throws SQLException {
        super.onOpen(sQLiteDatabase);
        sQLiteDatabase.execSQL(b.a.a().toString());
        sQLiteDatabase.execSQL(b.a.a().toString());
        sQLiteDatabase.execSQL(b.a.a().toString());
    }
}
```

```java
/* com/inmobi/commons/core/d/b.java */

    public final synchronized int a(String str) {
        int i;
        try {
            Cursor cursorRawQuery = this.f12548f.rawQuery("SELECT COUNT(*) FROM " + str + " ; ", null); // String no sanitizado dentro de la query
            cursorRawQuery.moveToFirst();
            i = cursorRawQuery.getInt(0);
            cursorRawQuery.close();
        } catch (Exception e2) {
            new StringBuilder("SDK encountered unexpected error in DbStore.getCount() method; ").append(e2.getMessage());
            return -1;
        }
        return i;
    }
  
  
    public final synchronized int b(String str, String str2, String[] strArr) {
        int i;
        try {
            Cursor cursorRawQuery = this.f12548f.rawQuery("SELECT COUNT(*) FROM " + str + " WHERE " + str2 + " ; ", strArr); // String no sanitizado dentro de la query
            cursorRawQuery.moveToFirst();
            i = cursorRawQuery.getInt(0);
            cursorRawQuery.close();
        } catch (Exception e2) {
            new StringBuilder("SDK encountered unexpected error in DbStore.getCount() method; ").append(e2.getMessage());
            return -1;
        }
        return i;
    }
```

<div class="page-break" style="page-break-before: always;"></div>

#### M5: Insufficient Cryptography
Se utiliza en múltiples ocasiones el algoritmo de hashing SHA-1, que se sabe débil y está descontinuado. 

```java
/* a/a/a/a/a/e/h.java */

byte[] digest = MessageDigest.getInstance("SHA1").digest(x509Certificate.getPublicKey().getEncoded());
```

```java
/* com/appsflyer/internal/ac.java */

MessageDigest messageDigest = MessageDigest.getInstance("SHA1");
```

```java
/* com/inmobi/commons/core/utilities/a/b.java */

Mac mac = Mac.getInstance("HmacSHA1");
```

<div class="page-break" style="page-break-before: always;"></div>

#### Credenciales filtradas
MobSF también detecta que debido a descuidos de los programadores, varias credenciales han quedado expuestas en el código. Podría ser un problema solucionado a día de hoy, pero podrían ser claves aún funcionales y activas.

```
"google_crash_reporting_api_key" : "AlzaSyBUhcPcZKinWWyEOXQg4_MCOEhhEBlqksE"
"google_api_key" : "AlzaSyBUhcPcZKinWWyEOXQg4_MCOEhhEBlqksE"
```

<div class="page-break" style="page-break-before: always;"></div>

### Servidores
Como se aprecia en la imagen, la app se comunica con servidores a lo largo y ancho del mundo, contactando a Europa, Estados Unidos, Rusia y China.

![[unof_map.png]]

### Trackers
La app viene también con su propia suite de trackers implementados, además de los invasores de privacidad habituales como son Facebook y Google, incluye algunos data brokers algo más oscuros y con un historial de prácticas y polémicas considerable, como Inmobi, Integral Ad Science o Moat. 

![[unof_trackers.png]]

<div class="page-break" style="page-break-before: always;"></div>

## Análisis dinámico
### Antes de tomar ninguna acción
La aplicación hace una serie de peticiones nada más abrirla, analizando la primera, vemos que se hace una petición por POST a un servidor de AWS, a juzgar por el dominio y el contenido del objeto JSON, se trata de una API Gateway, probablemente de un servicio de tracking, al que se envían datos sobre la versión de la app, la fecha de insatalación, el "proveedor" de la instalación y el idioma.

```c
POST /snap_api/snap_rc HTTP/1.1
Content-Type: application/x-www-form-urlencoded
User-Agent: Dalvik/2.1.0 (Linux; U; Android 7.0; Nexus 5X Build/NBD92Y)
Host: 2zny5tbbnh.execute-api.us-east-1.amazonaws.com
Connection: keep-alive
Accept-Encoding: gzip, deflate, br
Content-Length: 228

{"Language":"en",
"af_status":"default",
"appVersion":13000317,
"installTime":1770051123899,
"installer":"none",
"networkType":"1",
"osVersion":24,
"packgeName":"com.ehawk.speedtest.netmaster",
"sdkVersion":24,
"uuid":"bb4339760f2094ed"}
```

Conforme pasan los minutos, la app hace periódicamente la misma solicitud exacta al mismo dominio.
![[unof_aws_reqs.png]]

<div class="page-break" style="page-break-before: always;"></div>

De nuevo una petición que envía información del dispositivo y la app, sin duda alguna a un servidor que procesa la información para propósitos de publicidad, a juzgar por el dominio (ads.mobup.com). A este dominio se hacen peticiones muy similares justo después de la presente bajo el párrafo.

```c
POST /m/ad HTTP/1.1
user-agent: Mozilla/5.0 (Linux; Android 7.0; Nexus 5X Build/NBD92Y; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/74.0.3729.186 Mobile Safari/537.36
accept-language: en-us
Content-Type: application/json; charset=UTF-8
Host: ads.mopub.com
Connection: keep-alive
Accept-Encoding: gzip, deflate, br
Content-Length: 370

{"cn":"Android","z":"-0500","dnt":"0","ct":"2","h":"1920","current_consent_status":"unknown","w":"1080","av":"1.30.0.317","dn":"unknown,Nexus 5X,vbox86p","bundle":"com.ehawk.speedtest.netmaster","mnc":"260","o":"p","id":"1bf2d667066e45db82a6c215b6cb3b2a","udid":"mopub:67d796ea-023a-43e0-94f1-8d6b96850dcf","nv":"5.3.0","mcc":"310","sc":"2.625","force_gdpr_applies":"0"}
```

<div class="page-break" style="page-break-before: always;"></div>

A continuación hay una petición a launches.appsflyer.com, otro servicio de publicidad y procesamiento de información, en este caso, el cuerpo de la solicitud está cifrado, pero es plausible que se trate de lo mismo que las dos solicitudes anteriores, a juzgar por el dominio al que se está contactando.

```c
POST /api/v4.11/androidevent?app_id=com.ehawk.speedtest.netmaster&buildnumber=4.11.0 HTTP/1.1
Content-Length: 1464
Content-Type: application/octet-stream
User-Agent: Dalvik/2.1.0 (Linux; U; Android 7.0; Nexus 5X Build/NBD92Y)
Host: launches.appsflyer.com
Connection: keep-alive
Accept-Encoding: gzip, deflate, br

dbz^`Qóá<záÅ@·¡ºoh%pu¬ìÅ5bI5æÇò§Y0°îÇ#¶R2t<tØóªÎMr¡J^SjÛÓ%ýá§^a±¤XÉ·çCü¿ÌÞÖÛþ3ü5Á|n*ØÍ­ñÖXÔø9#Ù(kÏ	\Y8ÔÄ1j«i.Pg§®§@ù|Ø_;p§êþÐÚ<bjNy&ÈÒÕÛÉ¯9ÁCO7)Ê¿} :Á¶?¯²óßyñ4¾C½öø¦Åwß'M°q#zd±»ÈÍÞE¹êæ°@}}óz;Àñ ä¾BW}§¸fbZ!9Íp4ñS)K¸%½?ÐQûÌx×Iúª§í77D²l
XÝÊXè>ÅMªü5ïÇ_¡ÕR^¸¹JçÆbVs¤é]êÔEÝ¡t¥%®@.¤ÿ®tñoy1_jÚÐÌ´;ÍgÇ ÄCk¨ýíöRâ¡ûå÷ã$ÇÒàéûjr_2¸
```

<div class="page-break" style="page-break-before: always;"></div>

Posteriormente, hay varias peticiones más a mopub, una de ellas llama la atención ya que en vez de ir al endpoint `/ad`, va al endpoint `/gdpr_sync`, la cual se encuentra bajo la imagen.

![[unof_more_mopub.png]]
```c
...

{"bundle":"com.ehawk.speedtest.netmaster",
"nv":"5.3.0","dnt":"0",
"id":"4b43b1108e9741bea8d3487d4bf4b052","current_consent_status":"unknown",
"force_gdpr_applies":"0"}
```

Vemos que aquí se envía el estado del consentimiento del usuario, y pasa una variable que probablemente se trate de un valor booleano (`true(1) | false(0)`) que controla si se aplican o no las políticas de la GDPR.

<div class="page-break" style="page-break-before: always;"></div>

Por último, y como se ve en la imagen, se hace una petición a otro servicio de publicidad y tracking más, de la empresa Tenjin. En este caso, la petición va vacía, pero hay unas credenciales Basic Auth encodeadas en Base64 en dicha petición.

```c
POST /v0/event HTTP/1.1
/* Credenciales para autenticar vía BasicAuth */
Authorization: Basic UFpTVTRKSkhQR1hXMVVNSDU1UjZEV1E2WEpKQ0dNRkE=
Content-Type: application/x-www-form-urlencoded

...
```

<div class="page-break" style="page-break-before: always;"></div>

Aún sin haber tomado ninguna acción en la app, tras enviar en el cliente de Burpsuite todas las solicitudes que estaban retenidas, la app hace una petición mediante GET a appsflyer, el destinatario de la petición cuyo cuerpo está cifrado. En base a la información recibida previamente por la petición POST (presuntamente, ya que iba cifrada).
En el cuerpo de la petición, hay una llave de nombre 'devkey' y lo que, presumiblemente es un identificador único del dispositivo, aunque bien podría ser también un identificador general del modelo.

```c
GET /install_data/v3/com.ehawk.speedtest.netmaster?devkey=JHRRnkJA32V9Rip9CBWRmM&device_id=1770051130233-361595342597395836 HTTP/1.1
Connection: keep-alive
User-Agent: Dalvik/2.1.0 (Linux; U; Android 7.0; Nexus 5X Build/NBD92Y)
Host: api.appsflyer.com
```

<div class="page-break" style="page-break-before: always;"></div>


### Tomando acciones
#### Test de velocidad
Al hacer el test de velocidad que hay en la app, se efectúan una retahíla de peticiones, muchas de ellas a dominios ya conocidos en el análisis del paso previo, y algunos nuevos. 

![[unof_speedtest_reqs.png]]

El dominio de AWS ya aparece en las peticiones anteriores, así como el dominio de mopub y appsflyer, las peticiones no se incluyen en el reporte porque son prácticamente idénticas a las ya analizadas. Se envía información de la app y el dispositivo a los respectivos servidores de los data brokers.

Por otro lado, aparecen dos nuevos dominios, uno de Microsoft y otro de "Flurry". El de Microsoft es igual de sencillo que confuso; por alguna razón trata de descargar Internet Explorer 11 para Windows 6 x64. Se comprueba el dominio y es legítimo de Microsoft.

```c
GET /download/7/1/7/7179A150-F2D2-4502-9D70-4B59EA148EAA/IE11-Windows6.1-x64-en-us.exe HTTP/1.1
Host: download.microsoft.com
Accept-Encoding: gzip, deflate, br
Accept: */ *
User-Agent: okhttp/3.2.0
Connection: keep-alive
```

<div class="page-break" style="page-break-before: always;"></div>

En cuanto al de data.flurry, se empiezan a ver cosas de dudosa legitimidad, la primera petición es similar a la segunda, solo que la segunda contiene mucha más información. Esta está codificada, y se convierte a texto natural con DeepSeek, vemos una llave de API en los headers, pero lo interesante es el cuerpo de la request, que contiene todo tipo de información sensible

```c
POST /v1/flr.do HTTP/1.1
Host: data.flurry.com
Content-Type: application/octet-stream
X-Flurry-Api-Key: X89WPPSKWQB2FT6B8F3X

...

[FLURRY PROTOCOL DATA - DECODED CONTENT]

=== SESSION DATA ===
Session ID: 1769696761918
Device: Nexus 5X (Android 7.0, Build: NBD92Y)
App: Aptoide (cm.aptoide.pt) v9.22.5.3 (build 12060)
Device ID: ANDbb4339760f2094ed
Installation ID: Wu4lzFo/SV28qftXUPGxIQ==
Location: America/New_York
Language: en-US
Network: Cellular (Android, carrier: 310260)

=== EVENTS TRACKED ===

1. EVENT: Aptoide Launch (ID: 3)
   Timestamp: 1769696762200
   Parameters:
     - Source: "Launcher" (decoded from: U291cmNl → TGF1bmNoZXI=)

2. EVENT: Flurry.ColdStartTime (ID: 4)
   Timestamp: 1769696762323
   Parameters (Base64 decoded):
     - fl.layout.time: "1162"
     - fl.resume.time: "773"
     - fl.layout.runtime.memory: "4681176"
     - fl.resume.system.memory: "13201408"
     - fl.layout.system.memory: "16302080"
     - fl.resume.runtime.memory: "5556672"

3. EVENT: Flurry.ScreenTime: (ID: 5)
   Timestamp: 1769696762324
   Type: Timed event (started)
   Parameters (Base64 decoded):
     - fl.layout.time: "413"
     - fl.current.screen: "MainActivity"
     - fl.resume.time: "24"

4. EVENT: Page_View (ID: 6)
   Timestamp: 1769696762666
   Parameters (Base64 decoded):
     - store: "aptoide_main"
     - fragment: "HomeContainerFragment"

5. EVENT: Page_View (ID: 7)
   Timestamp: 1769696762837
   Parameters (Base64 decoded):
     - store: "aptoide_main"
     - fragment: "HomeFragment"

6. EVENT: Page_View (ID: 8)
   Timestamp: 1769696763376
   Parameters (Base64 decoded):
     - store: "aptoide_main"
     - fragment: "WizardPageOneFragment"

7. EVENT: Aptoide_First_Launch (ID: 9)
   Timestamp: 1769696763471
   Parameters (Base64 decoded):
     - UTM Content: "unknown"
     - UTM Campaign: "unknown"
     - UTM Medium: "unknown"
     - Entry Point: "unknown"
     - UTM Source: "unknown"

8. EVENT: Google_Play_Protect (ID: 10)
   Timestamp: 1769696764015
   Parameters (Base64 decoded):
     - is_active: "false"
     - flagged: "false"

9. EVENT: Page_View (ID: 11)
   Timestamp: 1769696765610
   Parameters (Base64 decoded):
     - store: "aptoide_main"
     - fragment: "HomeContainerFragment"

10. EVENT: Page_View (ID: 12)
    Timestamp: 1769696765611
    Parameters (Base64 decoded):
      - store: "aptoide_main"
      - fragment: "HomeFragment"

11. EVENT: Flurry.ForegroundTime (ID: 13)
    Timestamp: 1769696768592
    Parameters (Base64 decoded):
      - fl.current.screen: "MainActivity"
      - fl.foreground.time: "6681"

12. EVENT: Flurry.ScreenTime: (ID: 5 - ENDED)
    Timestamp: 1769696768593
    Type: Timed event (ended)
    Duration: 6269 ms
    Parameters (Base64 decoded):
      - fl.layout.time: "413"
      - fl.current.screen: "MainActivity"
      - fl.resume.time: "24"
      - fl.duration: "6682"

=== SESSION END (First) ===
Session end timestamp: 1769696768603
Duration: ~66.8 seconds

=== SESSION RESUME ===
Session resume timestamp: 1769696776350

13. EVENT: Aptoide Launch (ID: 15)
    Timestamp: 1769696776338
    Parameters:
      - Source: "Launcher"

14. EVENT: Flurry.ForegroundTime (ID: 16)
    Timestamp: 1769696776341
    Parameters (Base64 decoded):
      - fl.background.time: "7748"
      - fl.current.screen: "MainActivity"

15. EVENT: Page_View (ID: 17)
    Timestamp: 1769696776372
    Parameters (Base64 decoded):
      - store: "aptoide_main"
      - fragment: "HomeContainerFragment"

16. EVENT: Page_View (ID: 18)
    Timestamp: 1769696776375
    Parameters (Base64 decoded):
      - store: "aptoide_main"
      - fragment: "HomeFragment"

17. EVENT: Flurry.ScreenTime: (ID: 19)
    Timestamp: 1769696776421
    Type: Timed event (started)
    Parameters (Base64 decoded):
      - fl.layout.time: "80"
      - fl.current.screen: "MainActivity"
      - fl.resume.time: "13"
      - fl.previous.screen: "MainActivity"

18. EVENT: Bottom_Navigation_Interact (ID: 20)
    Timestamp: 1769696778211
    Parameters (Base64 decoded):
      - view: "search"

19. EVENT: Search_Start (ID: 21)
    Timestamp: 1769696778213
    Parameters (Base64 decoded):
      - source: "vanilla_bottom_nav"

20. EVENT: Page_View (ID: 22)
    Timestamp: 1769696778310
    Parameters (Base64 decoded):
      - store: "aptoide_main"
      - fragment: "SearchResultFragment"

21. EVENT: Search (ID: 23)
    Timestamp: 1769696785683
    Parameters (Base64 decoded):
      - inserted_keyword: "free crypto coins"
      - search_term: "free crypto coins"
      - search_term_source: "manual"

22. EVENT: Page_View (ID: 24)
    Timestamp: 1769696785831
    Parameters (Base64 decoded):
      - store: "aptoide_main"
      - fragment: "SearchResultFragment"

23. EVENT: Search_Results_App_View_Click (ID: 25)
    Timestamp: 1769696790180
    Parameters (Base64 decoded):
      - package_name: "ge.droid.freecryptocoins"
      - search_term: "free crypto coins"
      - is_ad: "false"
      - position: "0"

24. EVENT: Page_View (ID: 26)
    Timestamp: 1769696790501
    Parameters (Base64 decoded):
      - store: "meta"
      - fragment: "AppViewFragment"

25. EVENT: App_Viewed_Open_From (ID: 27)
    Timestamp: 1769696791489
    Parameters (Base64 decoded):
      - trusted_badge: "TRUSTED"
      - fragment: "SearchResultFragment"
      - package_name: "ge.droid.freecryptocoins"
      - store: "aptoide_main"
      - appcoins_type: "None"
      - application_publisher: "Militaria.Ge"

26. EVENT: Similar_App_Interact (ID: 28)
    Timestamp: 1769696799633
    Parameters (Base64 decoded):
      - Is_ad: "false"
      - Action: "impression"

27. EVENT: Appc_Similar_App_Interact (ID: 29)
    Timestamp: 1769696799634
    Parameters (Base64 decoded):
      - Is_ad: "false"
      - action: "impression"

28. EVENT: Page_View (ID: 30)
    Timestamp: 1769696806380
    Parameters (Base64 decoded):
      - store: "aptoide_main"
      - fragment: "SearchResultFragment"

29. EVENT: Bottom_Navigation_Interact (ID: 31)
    Timestamp: 1769696810954
    Parameters (Base64 decoded):
      - view: "search"

30. EVENT: Search_Start (ID: 32)
    Timestamp: 1769696810954
    Parameters (Base64 decoded):
      - source: "vanilla_bottom_nav"

31. EVENT: Search_Results_App_View_Click (ID: 33)
    Timestamp: 1769696817402
    Parameters (Base64 decoded):
      - package_name: "ge.droid.freecryptocoins"
      - search_term: "free crypto coins"
      - is_ad: "false"
      - position: "0"

32. EVENT: Page_View (ID: 34)
    Timestamp: 1769696817652
    Parameters (Base64 decoded):
      - store: "meta"
      - fragment: "AppViewFragment"

33. EVENT: App_Viewed_Open_From (ID: 35)
    Timestamp: 1769696818211
    Parameters (Base64 decoded):
      - trusted_badge: "TRUSTED"
      - fragment: "SearchResultFragment"
      - package_name: "ge.droid.freecryptocoins"
      - store: "aptoide_main"
      - appcoins_type: "None"
      - application_publisher: "Militaria.Ge"

=== FINAL SESSION EVENTS ===

34. EVENT: Flurry.ForegroundTime (ID: 36)
    Timestamp: 1769697352319
    Parameters (Base64 decoded):
      - fl.current.screen: "MainActivity"
      - fl.foreground.time: "575977"

35. EVENT: Flurry.ScreenTime: (ID: 19 - ENDED)
    Timestamp: 1769697352319
    Type: Timed event (ended)
    Duration: 575898 ms
    Parameters (Base64 decoded):
      - fl.layout.time: "80"
      - fl.resume.time: "13"
      - fl.previous.screen: "MainActivity"
      - fl.current.screen: "MainActivity"
      - fl.duration: "575978"

=== FINAL SESSION END ===
Session end timestamp: 1769697352333
Total session duration: ~590.4 seconds (9.84 minutes)
Total events: 35
App state transitions: Active → Background → Active → Background

=== SYSTEM METRICS ===
Memory: ~1.6-1.7 GB available
Battery: 100% (charging)
Storage: External ~3.2 GB, Internal ~297 MB
Network: Cellular (Android, signal: -100 dBm)
```

Recoge eventos, aplicaciones, información del sistema, estado de la batería, búsquedas previas... Casi con total seguridad para propósitos de publicidad o incluso inteligencia. En ningún momento se ha solicitado consentimiento más allá de aceptar las condiciones de uso, pero aún al margen de lo que puedan contener, esto es totalmente excesivo e ilegal en la UE.

<div class="page-break" style="page-break-before: always;"></div>

#### Otras funcionalidades
Una vez analizada la primera, lo demás resulta repetitivo, los dominios y el cuerpo se repiten, van actualizando información pero son prácticamente idénticos. Algunas peticiones son periódicas y se efectúan cada ciertos segundos, y otras solo al tomar acciones o ejecutar funcionalidades, pero los patrones se repiten.

![[unof_list_1.png]]

<div class="page-break" style="page-break-before: always;"></div>

En el caso del análisis de seguridad, aparecen nuevos dominios. TCLclouds es de nuevo un proveedor de tracking y telemetría, la petición a Baidu es confusa, ya que está vacía.

![[unof_list_2.png]]

Se continúa probando el resto de la funcionalidad de la app y las peticiones siguen el mismo patrón siempre, en definitiva, se trata de una aplicación que sin ser explícitamente malware, sirve principalmente para recopilar toda la información posible del usuario y venderla a publicistas y data brokers.

<div class="page-break" style="page-break-before: always;"></div>

# APK oficial (Waze)
Para la última fase del proyecto, se escoge la aplicación Waze, una app de navegación y mapas, principalmente debido al habitual uso personal que le doy a dicha app. Al igual que con las dos APKs anteriores, se analizará de forma estática mediante MobSF y Jadx la aplicación, y posteriormente se efectuará un análisis dinámico con Burpsuite como proxy.

Por problemas de incompatibilidad con las arquitecturas disponibles en las APK de Waze (solo disponibles para arm64) en el emulador Genymotion, cuya versión Desktop solo soporta arquitecturas x86_64, las pruebas de dicha APK se llevan a cabo en mi propio dispositivo Android.


## Reconocimiento inicial
La APK puntúa 52 en el análisis de MobSF, con 2 trackers detectados, en cuanto al análisis de VirusTotal, la aplicación es detectada por 0 de 62 antivirus. Esto contrasta con el análisis del APK previo, al cual se le detectaban 12 trackers y era detectada como malware por dos antivirus. Llama la atención que Waze, pese a ser una aplicación muy consumida y con buena reputación, solo consigue 4 puntos más en la puntuación de seguridad, pero eso se analizará en más profundidad ahora.

![[waze_score.png]]
![[waze_virustotal.png]]

<div class="page-break" style="page-break-before: always;"></div>

## Análisis estático
### Firma
En la firma empieza a verse que se trata de una aplicación más seria y preocupada por la seguridad que aplicaciones anteriores, ya que en lugar de usar versiones anticuadas e inseguras firma con la versión 3. Utiliza también certificado X.509, estándar de la ITU para llaves públicas, firmada con el país y región de la sede y el nombre director técnico entre septiembre de 2008 y enero de 2018.
La validez es vigente hasta 2063 y emplea DSA como método criptográfico, lo cual remarca la seguridad.

![[waze_sign.png]]

<div class="page-break" style="page-break-before: always;"></div>

### Manifiesto
La aplicación requiere un total de 42 permisos, debido a la longitud de la lista solo se analizarán los que MobSF detecta como peligrosos.

| **Permiso**                | **Función**                                                                                               | **Comentario**                                                                                                                                      |
| -------------------------- | --------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| ACCESS_BACKGROUND_LOCATION | Permite acceder a la localización en segundo plano                                                        |                                                                                                                                                     |
| ACCESS_COARSE_LOCATION     | Permite acceder a la localización aproximada (por ejemplo la proporcionada por la red celular)            |                                                                                                                                                     |
| ACCESS_FINE_LOCATION       | Permite acceder a la localización precisa, como la proporcionada por GPS                                  | Tanto esta como las dos anteriores son algo necesario para una app de navegación                                                                    |
| BLUETOOTH_CONNECT          | Permite conectarsea dispositivos Bluetooth emparejados                                                    |                                                                                                                                                     |
| BLUETOOTH_SCAN             | Permite descubrir y emparejarse con dispositivos cercanos vía Bluetooth                                   | Tanto esta como la anterior son utilizadas por Waze para conectarse a sistemas de entretenimiento/música/manos libres del vehículo                  |
| CAMERA                     | Permite tomar fotos y vídeos                                                                              | Waze incluye una funcionalidad para sacar fotos a los destinos para ayudar a otros usuarios                                                         |
| POST_NOTIFICATIONS         | Permite enviar notificaciones                                                                             | Razonable, ya que la funcionalidad que permite programar viajes envía notificaciones para salir a tiempo                                            |
| READ_CALENDAR              | Permite leer eventos almacenados en el/los calendarios del dispositivo                                    | Relacionado a la funcionalidad mencionada en el anterior permiso, se puede sincronizar el calendario con Waze para programar viajes automáticamente |
| READ_CONTACTS              | Permite leer la información almacenada de contactos en el dispositivo                                     | Quizá sea algo excesivo, casi con total seguridad se utiliza para gestionar llamadas con manos libres                                               |
| READ_PHONE_STATE           | Permite acceder a información crítica como número de serie, de teléfono, el estado de llamadas activas... | Relacionado al anterior, Waze modifica su comportamiento a nivel de audio al haber una llamada activa, de todas formas resulta algo excesivo        |
| READ_PROFILE               | Permite leer información sobre el perfil personal del usuario                                             | Relacionada con los dos permisos anteriores                                                                                                         |
| RECORD_AUDIO               | Permite la grabación de audio                                                                             | Waze permite la grabación de notas de voz como alertas para otros conductores                                                                       |
| SYSTEM_ALERT_WINDOW        | Permite mostrar ventanas de alerta                                                                        | Algo excesivo, sería suficiente con las notificaciones                                                                                              |

MobSF detecta algunos otros problemas en la aplicación en lo que se refiere al manifiesto. Ninguno grave. La mayoría de estas alertas surgen de variables `[android:exported=true]`, que son detectadas como vulnerables por no estar protegidas, sin embargo, tras revisarlas se comprueba que ninguna supone una brecha de seguridad

```java
        <receiver
            android:name="com.waze.CalendarChangeReceiver"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.PROVIDER_CHANGED"/>
                <data android:scheme="content"/>
                <data android:host="com.android.calendar"/>
            </intent-filter>
        </receiver>
```

<div class="page-break" style="page-break-before: always;"></div>

### Android API
Evidentemente, a nivel API lo más utilizado por la app es la ubicación por GPS, hay otras llamadas bastante estándar teniendo en cuenta que es una aplicación que recibe la información de los mapas en línea, u otras cosas más que razonables como pegar cosas del portapapeles

![[waze_androidAPI.png]]

<div class="page-break" style="page-break-before: always;"></div>

### Código fuente
En cuanto al código fuente, MobSF genera tan solo dos alertas de nivel grave.

![[waze_high_sc.png]]

La primera avisa de que el debugging WebView remoto está activado, aunque esto depende de una variable `z10` y la build si está desactivada como una versión debug.

![[waze_webview.png]]

Por otro lado, la segunda avisa de la utilización de un algoritmo criptográfico arriesgado/obsolescente. Esto supone, efectivamente una vulnerabilidad, ya que los algoritmos, aún siendo utilizados juntos, son vulnerables y pueden ser crackeados

```java
    public String mo34295b(String str) throws InvalidKeySpecException, NoSuchPaddingException, NoSuchAlgorithmException, InvalidKeyException, InvalidAlgorithmParameterException {
        try {
            byte[] bArrM1228d = str != null ? AbstractC0449a.m1228d(str) : new byte[0];
            SecretKey secretKeyGenerateSecret = SecretKeyFactory.getInstance("PBEWithMD5AndDES").generateSecret(new PBEKeySpec(f36029c));
            Cipher cipher = Cipher.getInstance("PBEWithMD5AndDES"); // Criptografía débil
            cipher.init(2, secretKeyGenerateSecret, new PBEParameterSpec(m34292d(), 20));
            return new String(cipher.doFinal(bArrM1228d));
        } catch (Exception e10) {
            this.f36031b.mo20884b("Cannot decrypt password: " + e10.getMessage());
            this.f36031b.mo20884b(Log.getStackTraceString(e10));
            return null;
        }
    }
```

### Servidores y dominios
La app se comunica con varios servidores en Estados Unidos, y uno en Indonesia. Esto tiene sentido ya que a pesar de que la sede está situada en Israel, la empresa fue comprada por Google hace años.

![[waze_map.png]]

Respecto a la lista de dominios a los que la app envía o recibe información, todos son de Google o Waze

<div class="page-break" style="page-break-before: always;"></div>

### Trackers
Respecto a los trackers, son básicamente servicios de telemetría de Google. No es ideal en términos de privacidad pero no es grave.

![[waze_trackers.png]]

<div class="page-break" style="page-break-before: always;"></div>

## Análisis dinámico
Por el problema mencionado previamente sobre la arquitectura del procesador, las pruebas de hacen directamente desde mi dispositivo personal, pasando el tráfico por Burp.

Si se deja el proxy interceptando el tráfico durante unos minutos, llama la atención que periódicamente, cada varias decenas de segundos, se hace una solicitud a dos dominios: `connectivitycheck.gstatic.com` y `play.googleapis.com`, ambas de ellas a un endpoint llamado `generate_204`.

![[waze_1_reqs.png]]

`gstatic` es un dominio de Google en el que se almacenan archivos estáticos como scripts JavaScript, imágenes y archivos CSS, con el propósito de mejorar la experiencia del usuario reduciendo los tiempos de carga a lo mínimo posible centralizando el lugar al que se solicitan dichos archivos. el subdominio `connectivitycheck` se utiliza, como su nombre indica, para verificar la conectividad del dispositivo cuando se intenta acceder a un servicio de Google. La solicitud de `googleapis.com` es casi con total seguridad, el servicio de Google que se está solicitando, y debido a la inestabilidad que genera la captura de tráfico se realizan también las peticiones a `connectivitycheck.gstatic.com`. 

 Según información de Internet sobre el dominio en cuestión, `connectivitycheck` se encarga también de tratar de alcanzar el dispositivo por vías alternativas, en este caso datos móviles. Al entrar a la aplicación, un popup que dice "¿Vas al trabajo?" aparece nada más entrar, si navegamos, Waze calcula la ruta sin problemas, comunicándose con los servidores de Waze "esquivando" el proxy, gracias a que `connectivitycheck` ha detectado que la conexión wifi era inestable y está contactando al dispositivo por datos móviles en lugar del wifi.

Ahora, se desactivan los datos para volver a acceder a la app y forzar al tráfico a pasar por el wifi. El móvil comienza a hacer decenas de peticiones a `192.168.1.1`, el default gateway del router (naturalmente). Esta vez, al entrar, no hay popup, por lo que se deduce que la información relativa a los hábitos de desplazamiento y las direcciones guardadas no se almacenan de forma local si no en los servidores de Waze. Si se intenta ir a un destino cualquiera, la aplicación sencillamente se queda colgada, no reacciona de ninguna manera, lo cual es natural debido a que Burp está interceptando todo el tráfico (en principio).

Sin embargo, si vamos a la pestaña de Burp donde cabría esperar que veríamos decenas de solicitudes a los servidores de Waze, no hay nada relacionado a la app.

![[waze_reqs_none.png]]

<div class="page-break" style="page-break-before: always;"></div>

### ¿Porque no hay tráfico de Waze?
Esto comienza a cobrar sentido cuando analizamos los logs de Burpsuite.

![[waze_error1.png]]
![[waze_error2.png]]
![[waze_error3.png]]
![[waze_error4.png]]

En dichos logs nos encontramos con decenas de intentos de solicitud hechos a distintos dominios de Waze, concretamente.
- `rt-xlb-row.waze.com`
- `wig-row.waze.com`
- `ctilesgcs-row.waze.com`
- `cresg.waze.com`

Apenas hay información online de ninguno de ellos, solo se sabe que están alojados en Google Cloud, como cabía esperar. No obstante, en lo que merece la pena centrarse es el propio mensaje de error: 
*"The client failed to negotiate a TLS connection to cresg.waze.com:443: Received fatal alert: certificate_unknown"*

Buscando información sobre este tipo de error, se llega a la conclusión de que el mecanismo que está impidiendo que Burpsuite pueda capturar el tráfico con normal (como si se puede hacer con otras aplicaciones del dispositivo) es el *cert pinning*. 
Por defecto, la mayoría de aplicaciones de Android comprueban la autenticidad del servidor al que hacen o del que reciben paquetes HTTPS consultando una lista de certificados presente en el dispositivo (para esto se instaló previamente un certificando de Burpsuite vía `adb`) en el dispositivo. La mayoría de aplicaciones utilizan esta lista estándar de certificados para establecer sus comunicaciones, sin embargo, algunas aplicaciones se salen de ese estándar, y utilizan su propia validación de certificados personalizada.

Resumidamente, la práctica consiste en consultar una lista preconfigurada de llaves públicas o certificados explícitamente confiados por la app para realizar sus comunicaciones, en vez de utilizar los certificados CA generales de dispositivo.

Evidentemente, Waze es una de esas aplicaciones cuyos métodos de verificación SSL/TLS está hecha a medida por la propia compañía, como sucede con otras aplicaciones que tienen detrás gigantes tecnológicos de tal tamaño como Instagram (Meta) o Twitch (Amazon). Esto, evidentemente supone una capa extra de seguridad al poder controlar a nivel más granular y específico que se permite y que no, aunque ahora la seguridad de Android al respecto se ha vuelto tan sólida con los años que el propio Google recomienda no incurrir en esta práctica (aunque todas las aplicaciones de Google que hay en mi teléfono sí lo hacen).

![[waze_google_certpin.png]]

Hay quien también piensa que es una forma de dificultar en análisis, no como una medida de hacer la aplicación en sí más segura, si no para añadir una capa extra de dificultad a aquellos que queremos entender como es que una aplicación se comunica con sus servidores/APIs.

<div class="page-break" style="page-break-before: always;"></div>

### Soluciones posibles
Gracias a al software frida, una herramienta para investigadores de seguridad, es posible bypassear el *cert pinning* y capturar el tráfico a través de Burpsuite, sin embargo, en este caso concreto a mí no me es posible, por una suma de razones.
Para poder evitar el *cert pinning* con frida, se requiere tener un dispositivo rooteado. Para mí ahora mismo no es posible porque he tenido que recurrir a mi dispositivo personal para hacer el análisis y es el único que tengo disponible, y no barajo la opción de rootearlo.

Evidentemente, existe la opción de utilizar una máquina virtual, sin embargo, hay un problema: en Genymotion desktop las imágenes tienen una arquitectura de procesador x86, y Waze solo tiene APKs para correr en procesadores arm64, Genymotion soporta traducción de x86 a ARM, pero solo hasta Android 9, y Waze requiere como mínimo Android 10 para descargarse. En Android Studio sí se pueden conseguir imágenes ARM, sin embargo, no soporta modo bridged en red, por lo que enrutar el tráfico a través de Burp es imposible.

En definitiva, sin un dispositivo rooteado, ya sea físico o virtual, es imposible esquivar el problema que supone el *cert pinning* a la hora de analizar el comportamiento de una aplicación a nivel red.





# Fuentes
- Android Docs
	- https://developer.android.com/guide/topics/manifest/manifest-intro
	- https://developer.android.com/reference/android/Manifest.permission
	- https://developer.android.com/reference/android/accounts/AccountManager
	- https://developer.android.com/reference/android/telecom/PhoneAccount
	- https://developer.android.com/build/build-variants
	- https://developer.android.com/reference/android/R
	- https://developer.android.com/reference/android/content/SyncStats
	- https://developer.android.com/reference/android/content/SharedPreferences
	- https://developer.chrome.com/docs/devtools/remote-debugging/webviews
	- https://developer.android.com/develop/ui/views/layout/webapps/debugging
	- https://developer.android.com/privacy-and-security/security-ssl#Pinning

- OWASP
	- https://mas.owasp.org/MASTG/tests/android/MASVS-RESILIENCE/MASTG-TEST-0224/

- NVD
	- https://nvd.nist.gov/vuln/detail/CVE-2017-13156

- GitHub
	- https://github.com/dineshshetty/Android-InsecureBankv2

- StackOverflow
	- https://stackoverflow.com/questions/63560102/android-permission-read-profile-in-android-what-it-does
	- https://stackoverflow.com/questions/32171204/android-studio-is-adding-android-permission-use-credentials-on-my-manifest
	- https://stackoverflow.com/questions/5181578/what-is-the-point-of-final-class-in-java
	- https://android.stackexchange.com/questions/44293/what-does-permission-manage-accounts-mean3
	
- https://www.guardsquare.com/blog/new-android-vulnerability-allows-attackers-to-modify-apps-without-affecting-their-signatures-guardsquare 
- https://commonsware.com/blog/2015/06/25/hey-where-did-these-permissions-come-from.html
- https://www.w3schools.com/java/java_classes.asp
- https://docs.oracle.com/javase/tutorial/java/javaOO/classes.html
- https://docs.oracle.com/javase/tutorial/java/javaOO/objects.html
- https://medium.com/@sashasinghh/role-of-r-files-in-android-development-4c1c0bb20cd0
- https://portswigger.net/burp/documentation/desktop/mobile/config-android-device
- https://en.wikipedia.org/wiki/X.509
- https://forum.netgate.com/topic/133398/http-connectivitycheck-gstatic-com-generate_204-error-with-https-login
- https://help.harmanpro.com/what-is-connectivitycheck-gstatic-com
- https://softwarekeep.com/blogs/what-is/what-is-gstatic-com
- https://www.reddit.com/r/chrome/comments/hh5rb6/what_is_gstaticcom/
- https://superuser.com/questions/1636190/the-url-http-www-gstatic-com-generate-204-is-opening-up-for-no-reason-in-chr
- https://www.reddit.com/r/hacking/comments/1dzw6ax/burp_certificate_unknown/
- https://httptoolkit.com/blog/frida-certificate-pinning/
- https://www.ssl.com/blogs/what-is-certificate-pinning/