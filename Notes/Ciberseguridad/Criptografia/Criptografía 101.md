# ¿Qué es la encriptación?
Que es un string o archivo encriptado y en qué se diferencia de un archivo cifrado/encodeado. Cifrado quiere decir que se ha usado algún algoritmo de cifrado pero no se ha usado una llave, es decir, el matiz clave para diferenciar uno de otro es el **uso de llaves**.

![[encoding-encryption-hasing-cheat-sheet.png|770x551]] ![[encoding-vs-encryption-vs-tokenization.png|552x552]]
## Encryption
Si nos referimos a un string o archivo como encodeado, esto implica el uso de un algoritmo de cifrado **que además requiere una llave**.
### ¿Qué es la encriptación?
La encriptación es una técnica que convierte la información en **ilegible** y difícil de descifrar para una persona que no posee la llave para ello.
Realmente, la encriptación es *una forma de cifrado*, sin embargo, hay una gran diferencia que los convierte en cosas totalmente distintas, aunque a menudo se confunden.
Los mecanismos de encriptación requieren, tanto para encriptar como desencriptar, algoritmos matemáticos que no pueden ser descifrados sin el uso de una llave (o con un poder computacional inmenso).
### Tipos de encriptación
- **Algoritmos de llave simétrica** (Symmetric-key algorithms): Estos algoritmos utilizan **la misma llave** para encriptar y desencriptar la información contenida. El algoritmo AES es un ejemplo.
- **Algoritmos de llave asimétrica** (Asymmetric-key algorithms): Este tipo de algoritmos utilizan **llaves distintas** para encriptar y desencriptar el contenido. Dichas llaves están relacionadas mediante matemáticas complejas, RSA es un ejemplo de encriptación asimétrica. 
![[asymmetric-encryption.png|450x220]] ![[symmetric-encryption.png|460x225]]
La encriptación es reversible, pero requiere la posesión de una llave para llevar a cabo esa desencriptación. De hecho, el propósito de estos algoritmos es complicar **lo máximo posible** la desencriptación de la información cifrada sin la llave necesaria para ello.
## Encoding / cifrado
Si nos referimos a un string o archivo como encodeado, esto implica el uso de un algoritmo de cifrado **sin el uso de una llave**.
### ¿Qué es el encoding?
Encoding es la acción de transformar datos de un formato a otro, es decir, cambia la forma de representar la información. Por ejemplo, cuando la información baja por las capas del modelo TCP/IP en algún punto pasa de ser lenguaje humano, como este mismo, a **encodearse** en binario, para ser proceso y enviado por ordenadores.
Algunos ejemplos podrían ser:
- **URL encoding:** `XD, You have been pwned` <--> `XD%2C%20You%20have%20been%20pwned` [URLencoder](https://www.urlencoder.org/)
- **base64 encoding**: `XD, Your have been pwned` <--> `WEQsIFlvdSBoYXZlIGJlZW4gcHduZWQ=` [[!Base64]]
Encodear no está pensado como una medida de seguridad extra, más bien como una forma de proporcionar códigos de comunicación entre sistemas, ya que cualquiera que pueda averiguar el método que se está utilizando puede descifrar el mensaje con mucha facilidad. Además, es totalmente reversible.

> “Every decoding is another encoding..”
> – David Lodge
## Hashing
### ¿Qué es el Hashing?
El hashing es una técnica para generar strings únicos de **longitud fija (hash)** en base a la información específica de entrada. Es decir, como el hash depende la información específica que lo genera, el más mínimo cambio genera un hash distinto.
Esto se traduce en que teniendo el hash de una pieza de información, se puede verificar la integridad de esta información calculando el hash y comparándolo con el que tenemos.
### Requisitos de un hash
- El hash resultante tiene una longitud fija
- El mismo input **siempre** produce el mismo output
- Distintos inputs **jamás** deberían producir el mismo output
- Debe ser irreversible; el input jamás debería poder ser obtenido a partir del output
### Tipos de algoritmos de Hashing
##### MD5
##### SHA (Secure Hashing Algorithm)
# Fuentes
[Encoding, Encryption and Hashing](https://youtu.be/-bAnBzvMLig)
[Como descubrir el tipo de cifrado](https://medium.com/hacking-info-sec/como-descubrir-el-tipo-de-cifrado-de-un-texto-encriptado-78d3a6f0bc2f)
[Artículo de 0Auth](https://auth0.com/blog/encoding-encryption-hashing/#What-Is-Encoding-)