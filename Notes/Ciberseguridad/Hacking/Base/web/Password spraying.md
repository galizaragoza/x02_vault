### ¿Qué es Password Spraying?
Un ataque Password Spraying es un tipo de ataque de fuerza bruta especialmente dirigido a entornos corporativos. Normalmente los ataques de fuerza bruta o diccionario se dan probando con muchas iteraciones por unidad de tiempo o bien la combinación de usuarios y contraseñas, o bien una vez se ha descubierto o se asume el usuario se prueba una gran lista de contraseñas (en el caso de fuerza bruta pura sería probar combinaciones de caracteres).

Aquí es donde el Password Spraying se diferencia de estos ataques, la idea es utilizar o confeccionar a mano (para mayor precisión) una lista de usuarios, la contraseña en este caso va a ser el valor fijo, y se utilizarán credenciales por defecto, contraseñas filtradas o comunes en entornos corporativos, y en esta ocasión será solo el nombre de usuario el valor sobre el que itera el ataque. 
Esto puede revelar no una si no varias cuentas válidas en un mismo entorno, como riesgo añadido, a menudo las cuentas proporcionadas en entornos corporativos suelen ser variaciones de los datos personales como nombre y apellidos, DNI... Por lo que si un atacante consigue reunir toda esta información mediante filtraciones, OSINT u otro método podría confeccionar una lista de cientos de variaciones posibles sobre las que sprayear.

![[passwd-spraying-attack.png]]

### Mitigación y prevención

### Fuentes
https://owasp.org/www-community/attacks/Password_Spraying_Attack
