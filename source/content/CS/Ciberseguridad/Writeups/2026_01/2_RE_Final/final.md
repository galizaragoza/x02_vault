# Proyecto final de ingeniería inversa en Máster de Ciberseguridad Atrium
## Por Mario Hinojosa
<div class="page-break" style="page-break-before: always;"></div>

# Dividiendo el código en basic blocks 
El código se divide el basic blocks mediante comentarios en el snippet bajo este párrafo, se divide en base a ciertos criterios el código en 7 bloques:
1. Prólogo
2. Declaración de variables y llamada a `strlen()`
3. Salto a 0x5ad (BB#5)
4. Bucle, recibe salto de BB#5 si la condición evalúa true y vuelve a BB#5
5. Finaliza con salto condicional, si evalúa true va al bucle (BB#4) y una vez evalúa false (o si lo hace en primera instancia) va a BB#6
6. Invoca `printf()` y avanza a BB#7
7. Epílogo y salida

```C
// ====================== BASIC BLOCK 1 ======================     
0x0000054d <+0>: lea ecx,[esp+0x4]
0x00000551 <+4>: and esp,0xfffffff0
0x00000554 <+7>: push DWORD PTR [ecx-0x4]
0x00000557 <+10>: push ebp
0x00000558 <+11>: mov ebp,esp
0x0000055a <+13>: push ebx
0x0000055b <+14>: push ecx
0x0000055c <+15>: sub esp,0x10
0x0000055f <+18>: call 0x450 <__x86.get_pc_thunk.bx>
0x00000564 <+23>: add ebx,0x1a9c
// Se identifica como BB#1 desde 0x54d hasta 0x564, compuesto de convenciones y configuración (prólogo)  



// ====================== BASIC BLOCK 2 ======================  
0x0000056a <+29>: mov DWORD PTR [ebp-0x10],0x0
0x00000571 <+36>: lea eax,[ebx-0x19a0] ; "3jd9cjfk98hnd"
0x00000577 <+42>: mov DWORD PTR [ebp-0x14],eax
0x0000057a <+45>: sub esp,0xc
0x0000057d <+48>: push DWORD PTR [ebp-0x14]
0x00000580 <+51>: call 0x3e0 strlen@plt
// Se identifica como BB#2 desde 0x56a hasta 0x580, el bloque inicializa una variable vacía y almacena el valor el string, luego invoca strlen (función de C para calcular longitud de un string)


// ====================== BASIC BLOCK 3 ======================  

0x00000585 <+56>: add esp,0x10  
0x00000588 <+59>: mov DWORD PTR [ebp-0x18],eax  
0x0000058b <+62>: mov DWORD PTR [ebp-0xc],0x0  
0x00000592 <+69>: jmp 0x5ad <main+96>
// Se define como BB#3 desde 0x585 hasta 0x592 debido al salto de 0x592 (apunta a 0x5ad, BB#5)


// ====================== BASIC BLOCK 4 ======================  
0x00000594 <+71>: mov edx,DWORD PTR [ebp-0xc]  
0x00000597 <+74>: mov eax,DWORD PTR [ebp-0x14]  
0x0000059a <+77>: add eax,edx  
0x0000059c <+79>: movzx eax,BYTE PTR [eax]  
0x0000059f <+82>: movsx eax,al  
0x000005a2 <+85>: imul eax,DWORD PTR [ebp-0x18]  
0x000005a6 <+89>: add DWORD PTR [ebp-0x10],eax  
0x000005a9 <+92>: add DWORD PTR [ebp-0xc],0x1  
// Recibe salto de BB#5 si la condición de 0x5b3 se cumple y finaliza linealmente


// ====================== BASIC BLOCK 5 ======================  
0x000005ad <+96>: mov eax,DWORD PTR [ebp-0xc]  
0x000005b0 <+99>: cmp eax,DWORD PTR [ebp-0x18]  
0x000005b3 <+102>: jl 0x594 <main+71>  
// Se define como BB#5 desde 0x5ad hasta 0x5b3 debido a que finaliza con un salto condicional a BB#4


// ====================== BASIC BLOCK 6 ======================   
0x000005b5 <+104>: sub esp,0x8  
0x000005b8 <+107>: push DWORD PTR [ebp-0x10]  
0x000005bb <+110>: lea eax,[ebx-0x1992] ; "[+] Codigo generado: %i\n"  
0x000005c1 <+116>: push eax  
0x000005c2 <+117>: call 0x3d0 printf@plt  
0x000005c7 <+122>: add esp,0x10  
0x000005ca <+125>: mov eax,0x0  
// Invoca la función printf() y continúa linealmente hacia BB#7


// ====================== BASIC BLOCK 7 ======================   
0x000005cf <+130>: lea esp,[ebp-0x8]  
0x000005d2 <+133>: pop ecx  
0x000005d3 <+134>: pop ebx  
0x000005d4 <+135>: pop ebp  
0x000005d5 <+136>: lea esp,[ecx-0x4]  
0x000005d8 <+139>: ret  
// Epílogo e instrucción de retorno
```

<div class="page-break" style="page-break-before: always;"></div>

# Realiza el diagrama de flujo
En base a la información de la sección anterior se evalúa un diagrama para apoyo visual

![[RE_final.jpg]]

<div class="page-break" style="page-break-before: always;"></div>

# Estructura de control y basic blocks
En el código analizado hay, sin lugar a dudas, estructuras de control.
Los bloques 1, 2 y 3 se suceden de manera secuencial, aparentemente no hay ninguna estructura de control, sin embargo, tras el salto del bloque 3 al bloque 5 nos encontramos con la primera (aunque realmente 5 y 4 son una única estructura de control).

Se calculan el total de caracteres en el string con `strlen()`, cuando el bloque 5 recibe el salto del bloque 3, el contador del bucle tiene un valor de 0 y este aumenta por cada carácter, hasta que el valor de dicho contador es mayor que el de la longitud del string, la condición evalúa como falsa y se sale del bucle.
En bucle, como tal, va pasando por cada uno de los caracteres del string, los convierte a un entero y los multiplica por la longitud total del string. Se he representado en un lenguaje inventado a continuación:

```c
codigo = 0 // Inicializa la variable código
string = "Lo que sea" // Se declara la variable string
longitud = calcular_longitud(string) // Se calcula la longitud del string con una función ficticia

for (int i = 0; i < length; i++) { // Se declara un contador (i) con un valor de cero, y la condición para que el bucle se siga ejecutando es que i sea menor que $longitud, por cada ejecución del bucle se suma 1 a i
    codigo += (int)str[i] * length; // Se suma a la variable $codigo el valor numerico del caracter actual multiplicado por $longitud, se suma 1 a i y se vuelve a ejecutar hasta que i < $longitud
}

printf(codigo)
```

El bloque 5 sería entonces el encargado de comprobar en cada ejecución del bucle si `i < longitud`, y el bloque 4 es el propio código del bucle, que convierte el carácter a un número entero, lo multiplica por `$longitud` y lo suma a `$codigo`. Entonces vuelve al bloque 5 y este vuelve a comprobar si `i < longitud`, así hasta que se cumple la condición y se pasa al bloque 6, y del 6 al 7.

<div class="page-break" style="page-break-before: always;"></div>

# Conversión a C
Se convierte el código a C con la esta [web](https://www.codeconvert.ai/assembly-to-c-converter).
```C
#include <stdio.h>
#include <string.h>

int main(int argc, char *argv[]) {
    char *str = "3jd9cjfk98hnd";
    int sum = 0;
    int length = strlen(str);

    for (int i = 0; i < length; i++) {
        sum += (int)str[i] * length;
    }

    printf("[+] Codigo generado: %i\n", sum);

    return 0;
}

```

<div class="page-break" style="page-break-before: always;"></div>

# Compila el código generado
Al compilar el binario (para este paso se uso el compilador `clang` en lugar de `gcc`)
```sh
clang -o bin src.c
```

Ejecutar el binario manda al stdout la siguiente cadena de texto. 

![[bin_output.png]]

<div class="page-break" style="page-break-before: always;"></div>

# Modifica el código fuente en C
Como se indica en el enunciado, se modifica la part del código C correspondiente a la posición <+36> en assembly por la cadena “Congratulations!”.

![[nvim_c.png]]

Una vez hecho esto, se compila y corre de nuevo el binario, obteniendo un output distinto.
![[2bin_output.png]]![[output_compare.png]]
Como se esperaba, el código cambia.

<div class="page-break" style="page-break-before: always;"></div>

<div class="page-break" style="page-break-before: always;"></div>
