#reference #Ciberseguridad

**Fuzzing** = enviar automáticamente entradas malformadas, aleatorias o inesperadas a un programa para provocar fallos (crashes, hangs, memory corruption) que revelen **vulnerabilidades**. Técnica clave en SecureDev y descubrimiento de bugs.

# Tipos

| Tipo | Idea |
|------|------|
| **Dumb / mutation** | Mutar entradas válidas al azar (bit flips, splices). |
| **Smart / generation** | Generar entradas según un modelo/gramática del formato. |
| **Coverage-guided** | Instrumenta el binario y prioriza inputs que abren código nuevo (AFL++, libFuzzer). |
| **Black/grey/white-box** | Sin/parcial/con acceso al código fuente. |

# Coverage-guided (el estándar moderno)

El fuzzer mide qué ramas ejecuta cada input y evoluciona un corpus hacia mayor cobertura → encuentra caminos profundos.

```bash
# AFL++ (binario instrumentado)
afl-cc -o target target.c          # compilar con instrumentación
afl-fuzz -i seeds/ -o out/ ./target @@

# libFuzzer (in-process, una función objetivo)
clang -fsanitize=fuzzer,address harness.c -o fuzzer
./fuzzer corpus/
```

```c
// Harness libFuzzer
int LLVMFuzzerTestOneInput(const uint8_t *data, size_t size) {
    parse_input(data, size);   // función bajo prueba
    return 0;
}
```

# Buenas prácticas

- **Sanitizers**: compilar con ASan/UBSan/MSan → detectan corrupción que no crashea.
- **Corpus semilla** representativo + minimización (`afl-cmin`, `afl-tmin`).
- **CI continuo**: OSS-Fuzz ejecuta fuzzing 24/7 sobre proyectos open source.
- Triaje de crashes: reproducir, deduplicar, evaluar explotabilidad.

# Fuzzing web (≠ fuzzing de binarios)

En web, "fuzzing" = probar payloads/rutas/parámetros con `ffuf`, `wfuzz`, Burp Intruder (descubrimiento de contenido, parámetros). Ver [[RECON_CS]].

# Recursos
### [[Buffer Overflow]] · [[RECON_CS]]
### [AFL++](https://github.com/AFLplusplus/AFLplusplus) · [libFuzzer](https://llvm.org/docs/LibFuzzer.html) · [OSS-Fuzz](https://github.com/google/oss-fuzz)
