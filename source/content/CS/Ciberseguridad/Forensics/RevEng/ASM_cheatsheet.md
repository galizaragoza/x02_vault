# Registros

## Registros de Propósito General (32-bit)

|Registro|Descripción|
|---|---|
|**EAX**|Contiene el valor de retorno de una llamada a función|
|**ECX**|Usado como contador de bucles. Puntero "this" en C++|
|**EBX**|Propósito general|
|**EDX**|Propósito general|
|**ESI**|Puntero de índice de origen (Source Index)|
|**EDI**|Puntero de índice de destino (Destination Index)|
|**ESP**|Puntero de pila (Stack Pointer)|
|**EBP**|Puntero base de pila (Stack Base Pointer)|

## Registros de Segmento

|Registro|Descripción|
|---|---|
|**CS**|Segmento de código (Code Segment)|
|**SS**|Segmento de pila (Stack Segment)|
|**DS**|Segmento de datos (Data Segment)|
|**ES**|Segmento de datos extra|
|**FS**|Apunta al Thread Information Block (TIB)|
|**GS**|Segmento de datos extra|

## Registros Misceláneos

|Registro|Descripción|
|---|---|
|**EIP**|Puntero de instrucción (Instruction Pointer)|
|**EFLAGS**|Banderas de estado del procesador|

## Banderas de Estado (EFLAGS)

|Bandera|Descripción|
|---|---|
|**ZF** (Zero Flag)|Operación resultó en cero|
|**CF** (Carry Flag)|Origen > destino en sustracción|
|**SF** (Sign Flag)|Operación resultó en número negativo|
|**OF** (Overflow Flag)|Resultado muy grande para el destino|

## Registros de 16-bit y 8-bit

|Registro 32-bit|Registro 16-bit|Registro 8-bit (High)|Registro 8-bit (Low)|
|---|---|---|---|
|**EAX**|AX|AH|AL|
|**EBX**|BX|BH|BL|
|**ECX**|CX|CH|CL|
|**EDX**|DX|DH|DL|

# Instrucciones
```c
INS OP1, OP2, OP3 // instruction [operand1, operand2, operand3]
// Algunas INS toman 1, 2, 3 o 0 operands
// Pueden tomar "immediate values" (1,2,3...) o posiciones relativas como “[%eax + 4]” (EAX +4 bytes)
```

## Instrucciones Aritméticas

|Instrucción|Sintaxis|Descripción|
|---|---|---|
|**ADD**|`ADD <dest>, <source>`|Suma `<source>` a `<dest>`|
|**SUB**|`SUB <dest>, <source>`|Resta `<source>` de `<dest>`|
|**MUL**|`MUL <source>`|Multiplica EDX:EAX por `<source>`|
|**DIV**|`DIV <divisor>`|Divide EDX:EAX por `<divisor>`|
|**INC**|`INC <dest>`|Suma 1 a `<dest>`|
|**DEC**|`DEC <dest>`|Resta 1 de `<dest>`|

## Instrucciones de Transferencia de Datos

| Instrucción | Sintaxis                | Descripción                                     |
| ----------- | ----------------------- | ----------------------------------------------- |
| **MOV**     | `MOV <dest>, <source>`  | Mueve datos de `<source>` a `<dest>`            |
| **LEA**     | `LEA <dest>, <source>`  | Carga dirección efectiva en `<dest>`            |
| **XCHG**    | `XCHG <dest>, <source>` | Intercambia contenidos de `<source>` y `<dest>` |
| **PUSH**    | `PUSH <value>`          | Empuja valor de 32-bit a la pila                |
| **POP**     | `POP <dest>`            | Saca valor de 32-bit de la pila                 |

## Instrucciones de Comparación y Saltos

|Instrucción|Sintaxis|Descripción|
|---|---|---|
|**CMP**|`CMP <dest>, <source>`|Compara `<source>` con `<dest>`|
|**TEST**|`TEST <dest>, <source>`|Realiza OR lógico sin modificar `<dest>`|
|**JMP**|`JMP <loc>`|Salto incondicional|
|**JE**|`JE <loc>`|Salta si igual (ZF=1)|
|**JNE**|`JNE <loc>`|Salta si no igual (ZF=0)|
|**JZ**|`JZ <loc>`|Salta si cero (ZF=1)|
|**JNZ**|`JNZ <loc>`|Salta si no cero (ZF=0)|
|**JG**|`JG <loc>`|Salta si mayor (ZF=0 y SF=OF)|
|**JGE**|`JGE <loc>`|Salta si mayor o igual (SF=OF)|
|**JLE**|`JLE <loc>`|Salta si menor o igual (SF<>OF)|

## Instrucciones de Desplazamiento y Rotación

|Instrucción|Sintaxis|Descripción|
|---|---|---|
|**SHL**|`SHL <dest>, <count>`|Desplazamiento lógico a izquierda|
|**SHR**|`SHR <dest>, <count>`|Desplazamiento lógico a derecha|
|**ROL**|`ROL <dest>, <count>`|Rotación a izquierda|
|**ROR**|`ROR <dest>, <count>`|Rotación a derecha|

## Instrucciones Lógicas

|Instrucción|Sintaxis|Descripción|
|---|---|---|
|**XOR**|`XOR <dest>, <source>`|XOR bit a bit|
|**CALL**|`CALL <loc>`|Llama a función|
|**RET**|`RET`|Retorna de función|

# La Pila (Stack)

## Estructura de la Pila

![[stack_asm.png]]

# Terminología del Lenguaje Ensamblador

## Directivas del Ensamblador

|Directiva|Descripción|
|---|---|
|**DB**|Define Byte - Reserva un byte explícito|
|**DW**|Define Word - Reserva 2 bytes|
|**DD**|Define DWord - Reserva 4 bytes|

## Tipos de Operandos

| Tipo          | Descripción                            |
| ------------- | -------------------------------------- |
| **Inmediato** | Un operando numérico, codificado       |
| **Registro**  | Un registro de propósito general       |
| **Memoria**   | Dirección de memoria con corchetes [ ] |
|               |                                        |

# Conceptos de PE (Portable Executable)

## Términos de PE

|Término|Descripción|
|---|---|
|**Pointer to Raw Data**|Offset de datos de sección dentro del archivo ejecutable|
|**Size of Raw Data**|Cantidad de datos de sección dentro del archivo ejecutable|
|**RVA**|Dirección Virtual Relativa (Relative Virtual Address)|
|**VA**|Dirección Virtual Absoluta (Virtual Address)|
|**Virtual Size**|Cantidad de datos de sección en memoria|
|**Base Address**|Offset en memoria donde se carga el módulo ejecutable|
|**ImageBase**|Dirección Base solicitada en el encabezado PE|
|**Module**|Archivo en formato PE cargado en memoria (EXE o DLL)|
|**Pointer**|Dirección de memoria|
|**Entry Point**|Dirección de la primera instrucción a ejecutar|
|**Import**|Funciones DLL requeridas por un ejecutable|
|**Export**|Funciones proporcionadas por una DLL|

# Fórmulas de Conversión

## Conversiones de Direcciones

| Conversión    | Fórmula                                                                    |
| ------------- | -------------------------------------------------------------------------- |
| **RVA → RAW** | `Raw = (RVA - SectionStartRVA) + (SectionStartRVA - SectionStartPtrToRaw)` |
| **RVA → VA**  | `VA = RVA + BaseAddress`                                                   |
| **VA → RVA**  | `RVA = VA - BaseAddress`                                                   |
| **RAW → VA**  | `VA = (Raw - SectionStartPtrToRaw) + (SectionStartRVA + ImageBase)`        |

https://www.cs.virginia.edu/~evans/cs216/guides/x86.html#registers
https://sammwy.com/blog/understanding-asm#registers

