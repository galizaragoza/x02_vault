El algoritmo de cifrado RSA (Rivest-Shamir-Adleman) es un criptosistema, normalmente nos referimos a él para hacer alusión al algoritmo de cifrado asimétrico o de llave pública, cuyo propósito es hacer los mensajes indescifrables para todo aquel que no posea las llaves. Aunque esto podría cambiar con la computación cuántica.

![[asym-encryption.png|560x314]] ![[asymmetric-encryption.png|627x314]]

El algoritmo relaciona con matemáticas complejas una llave pública a una privada que son el producto de la multiplicación de dos enormes números primos. Es sencillo multiplicar dos primos pero complicado factorizar su producto, por ejemplo $17 * 23 = 391$ es fácil, pero $391 = 17*23$ no tanto, aún menos cuando estos primos tienen cientos de dígitos.

Las llaves que utiliza RSA van de 2048 bits a 4096 bits.

## Como funciona
1. Se comienza el algoritmo escogiendo dos números primos (en la vida real son primos ridículamente altos, de cientos de dígitos, y así debe ser para que funcione). Para este ejemplo serán 2 y 7 ($p$ y $q$).

2. Se multiplican ambos números 
	- $p * q = N$
	- En el ejemplo: $2 * 7 = 14$

3. Función de Euler o función totiente --> $ϕ(N)$
	- $ϕ (N) = (p-1)*(q-1)$
	- En ejemplo, $ϕ (14) = 6$

4.  Ahora es el momento de generar la llave de cifrado ($E$), debe cumplir dos propiedades
	- Debe estar entre 1 y el resultado de la función de Euler --> $1 < E < ϕ (N)$
	- Debe ser coprimo de $N$ y $ϕ (N)$ 
		Un número se considera coprimo de otro cuando no comparten ningún divisor común salvo el 1 
	- En el ejemplo, $E = 5$

A estas alturas del algoritmo, es cuando tenemos la **llave pública**, que es básicamente la conjunción de $E$ y $N$, $PublicKey = (E, N)$, en este ejemplo, $PublicKey = (5, 14)$.
	PuK = public key

5. Ahora es momento de escoger $D$, nuestra llave para descifrar, en base a unas propiedades.
	- Para hallar D, hay que despejar una ecuación con varios elementos obtenidos previamente. $D * E (mod$ $ϕ (N)) = 1$
	- $5 * D (mod$ $6) = 1$, en este ejemplo, se escogerá el 11. $D = 11$ 

La llave privada, similar a la pública, es una conjunción de $D$ y $N$, quedando $PrivateKey = (D, N)$, en el ejemplo: $PrivateKey = (11, 14)$. 

Luego, con estas llaves, se encripta tal que así
6. Dada una información X inicial, el primer paso es codificarla.
	- Por ejemplo, B será la info y 2 su codificación (por su posición en el alfabeto)
	- Información codificada = $X$, información cifrada = $Xe$

7. $X^E (mod$ $N) = Xe$, en el ejemplo $2^5(mod$ $14) = 4$

8. $Xe^D(mod$ $N) = X$, en el ejemplo $4^11(mod$ $14)=2$ --> 2 = B (información original)


### Resumido

1. $p$ & $q$ --> grandes números primos

2. $p * q = N$

3. $ϕ (N) = (p-1)(q-1)$

4. $E$
	- $1 < E < ϕ (N)$
	- Coprimo de $N$ y $ϕ (N)$ 

5. $PublicKey = (E, N)$

6. $D$
	- $D * E (mod$ $ϕ (N)) = 1$

7. $PrivateKey = (D, N)$

Para cifrar/descifrar información

8. $X$ = información codificada, $Xe$ = información cifrada
 
9. $X^E (mod$ $N) = Xe$

10. $Xe^D(mod$ $N) = X$

## Recursos
[# The RSA Encryption Algorithm (1 of 2: Computing an Example)](https://youtu.be/4zahvcJ9glg)
[# The RSA Encryption Algorithm (2 of 2: Generating the Keys)](https://youtu.be/oOcTVTpUsPQ)
[# Quantum computing RSA encryption: a threat and a solution](https://freemindtronic.com/quantum-computing-rsa-encryption-freemindtronic-nfc-technology/)