#reference

**C** = lenguaje compilado de bajo nivel, tipado estático, sin gestión automática de memoria. Base de SOs, drivers y exploits. Su control manual de memoria es potente pero fuente de vulnerabilidades ([[Buffer Overflow]]).

# Estructura mínima

```c
#include <stdio.h>

int main(void) {
    printf("hola\n");
    return 0;
}
```

```bash
gcc prog.c -o prog          # compilar
gcc -Wall -g prog.c -o prog # warnings + símbolos de debug
./prog
```

# Tipos

| Tipo | Nota |
|------|------|
| `char` | 1 byte (también para bytes). |
| `int`, `short`, `long`, `long long` | Enteros con signo. |
| `unsigned ...` | Sin signo. |
| `float`, `double` | Coma flotante. |
| `void` | Sin valor / puntero genérico. |
| `size_t` | Tamaños (sin signo). |

# Punteros y memoria

```c
int x = 5;
int *p = &x;        // p apunta a x
*p = 10;            // x ahora vale 10

// Memoria dinámica (heap)
int *arr = malloc(10 * sizeof(int));
if (arr == NULL) { /* error */ }
free(arr);          // ¡liberar siempre!
```

> Errores clásicos (y vulnerabilidades): *buffer overflow*, *use-after-free*, *double free*, *off-by-one*, fugas de memoria, desreferenciar `NULL`.

# Control de flujo

```c
if (x > 0) { ... } else { ... }
for (int i = 0; i < n; i++) { ... }
while (cond) { ... }
switch (x) { case 1: ...; break; default: ...; }
```

# Funciones y structs

```c
int suma(int a, int b) { return a + b; }

struct Punto { int x, y; };
struct Punto p = {1, 2};
```

# Strings (arrays de char)

```c
char s[] = "texto";        // terminado en '\0'
strlen(s); strcpy(d, s); strcmp(a, b);   // <string.h>
// Preferir variantes con tamaño: strncpy, snprintf (evitan overflow)
```

# Seguridad

Funciones peligrosas (sin control de tamaño): `gets`, `strcpy`, `strcat`, `sprintf`, `scanf("%s")`. Usar las versiones con límite (`fgets`, `strncpy`, `snprintf`). Compilar con `-fstack-protector`, ASLR, NX. Analizar con `valgrind`, ASan (`-fsanitize=address`).

# Recursos
### [[Buffer Overflow]] · [[ASM_cheatsheet]]
### [Beej's Guide to C](https://beej.us/guide/bgc/) · [cppreference — C](https://en.cppreference.com/w/c)
