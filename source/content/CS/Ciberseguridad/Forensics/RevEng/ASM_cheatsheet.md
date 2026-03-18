# Registros

## Registros de Propósito General (32-bit)

| **Registro** | **Nombre**        | **Descripción Extendida**                                                                                                     |
| ------------ | ----------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| **EAX**      | Accumulator       | El registro principal para operaciones aritméticas y el lugar estándar donde las funciones depositan su **valor de retorno**. |
| **ECX**      | Counter           | Utilizado automáticamente como contador en bucles (`LOOP`) y operaciones de cadenas. En C++, suele pasar el puntero `this`.   |
| **EBX**      | Base              | Históricamente usado como puntero a datos en el segmento DS. Hoy es un registro de propósito general "preservado".            |
| **EDX**      | Data              | Extensión del acumulador. Se usa en multiplicaciones/divisiones complejas y para definir puertos de entrada/salida (I/O).     |
| **ESI**      | Source Index      | Puntero de origen para operaciones de copia de memoria o strings (ej. `MOVSB`).                                               |
| **EDI**      | Destination Index | Puntero de destino para operaciones de copia de memoria o strings.                                                            |
| **ESP**      | Stack Pointer     | **Puntero dinámico**: Apunta siempre al "tope" actual de la pila (la dirección más baja ocupada).                             |
| **EBP**      | Base Pointer      | **Puntero estático**: Apunta a la base del "stack frame" de la función actual para localizar variables locales y parámetros.  |


## Registros de segmento

| **Registro** | **Categoría** | **Descripción**                                                                 |
| ------------ | ------------- | ------------------------------------------------------------------------------- |
| **CS**       | Segmento      | **Code Segment**: Ubicación del código que se está ejecutando.                  |
| **DS / ES**  | Segmento      | **Data / Extra Segment**: Ubicación de las variables y datos globales.          |
| **SS**       | Segmento      | **Stack Segment**: Ubicación de la pila en memoria.                             |
| **FS / GS**  | Segmento      | Segmentos extra. En Windows, **FS** apunta al Thread Information Block (TIB).   |
| **EIP**      | Control       | **Instruction Pointer**: Contiene la dirección de la **siguiente instrucción**. |
| **EFLAGS**   | Control       | Registro de 32 bits donde cada bit es una "bandera" de estado.                  |

## Banderas de Estado (EFLAGS)

| **Bandera** | **Nombre**    | **Se activa (1) cuando...**                                            |
| ----------- | ------------- | ---------------------------------------------------------------------- |
| **ZF**      | Zero Flag     | El resultado de la operación fue exactamente **cero**.                 |
| **CF**      | Carry Flag    | Hubo un acarreo o préstamo (útil para aritmética sin signo).           |
| **SF**      | Sign Flag     | El resultado es negativo (el bit más significativo es 1).              |
| **OF**      | Overflow Flag | El resultado excedió la capacidad del registro (aritmética con signo). |

## Registros de 16-bit y 8-bit

| **32-bit (Extendido)** | **16-bit (Bajo)** | **8-bit (High)** | **8-bit (Low)** |
| ---------------------- | ----------------- | ---------------- | --------------- |
| **EAX**                | AX                | AH               | AL              |
| **EBX**                | BX                | BH               | BL              |
| **ECX**                | CX                | CH               | CL              |
| **EDX**                | DX                | DH               | DL              |

# Instrucciones
```c
INS OP1, OP2, OP3 // instruction [operand1, operand2, operand3]
// Algunas INS toman 1, 2, 3 o 0 operands
// Pueden tomar "immediate values" (1,2,3...) o posiciones relativas como “[%eax + 4]” (EAX +4 bytes)
```

## Instrucciones aritméticas y de transferencia

| **Instrucción** | **Sintaxis**           | **Descripción**                                                        |
| --------------- | ---------------------- | ---------------------------------------------------------------------- |
| **MOV**         | `MOV dest, src`        | Copia el valor de `src` a `dest`. No afecta las banderas.              |
| **LEA**         | `LEA dest, src`        | **Load Effective Address**: Calcula la dirección de `src` y la guarda. |
| **PUSH / POP**  | `PUSH val` / `POP reg` | Mete o saca valores de la pila (modifica automáticamente `ESP`).       |
| **ADD / SUB**   | `ADD dest, src`        | Suma o resta. El resultado se guarda en `dest`.                        |
| **INC / DEC**   | `INC dest`             | Suma o resta **1** al operando de forma eficiente.                     |
| **MUL / DIV**   | `MUL src`              | Multiplicación/División implícita usando EAX y EDX.                    |

## Instrucciones de control de flujo

| **Instrucción** | **Sintaxis**    | **Descripción**                                                                    |
| --------------- | --------------- | ---------------------------------------------------------------------------------- |
| **CMP**         | `CMP op1, op2`  | Resta `op2` de `op1` solo para actualizar banderas (no guarda el resultado).       |
| **TEST**        | `TEST op1, op2` | Realiza un `AND` lógico para actualizar banderas (ideal para ver si algo es cero). |
| **JMP**         | `JMP loc`       | Salto incondicional a una dirección de memoria.                                    |
| **JE / JNE**    | `JE loc`        | Salta si es igual (ZF=1) o si no es igual (ZF=0).                                  |
| **JG / JL**     | `JG loc`        | Salta si es Mayor (Greater) o Menor (Less).                                        |
| **CALL / RET**  | `CALL func`     | Llama a una función (guarda retorno en pila) o regresa de ella.                    |

## Instrucciones lógicas y de desplazamiento

|**Instrucción**|**Sintaxis**|**Descripción**|
|---|---|---|
|**XOR**|`XOR reg, reg`|Muy usado para poner un registro a cero de forma rápida (`XOR EAX, EAX`).|
|**SHL / SHR**|`SHL dest, count`|Desplaza bits a la izq/der. Equivale a multiplicar/dividir por $2^n$.|
|**ROL / ROR**|`ROL dest, count`|Rotación de bits: los que salen por un lado entran por el otro.|

# La Pila (Stack)

## Estructura de la Pila

![[stack-vs-heap.png|875|1217x644]]![[stack_asm.png|415x301]]

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

