#reference #Ciberseguridad #IA

Uso de **LLMs como apoyo en reverse engineering**: acelerar el análisis de código desensamblado/decompilado pidiendo a un modelo que explique funciones, renombre variables, infiera estructuras o resuma comportamiento. Acelera el triaje; no sustituye la verificación humana.

# Para qué sirve

| Tarea | Cómo ayuda el LLM |
|-------|-------------------|
| Explicar una función | Resume qué hace un bloque de pseudocódigo Ghidra/IDA. |
| Renombrar | Sugiere nombres significativos para vars/funcs. |
| Inferir structs | Deduce campos a partir de accesos a memoria. |
| Identificar algoritmos | Reconoce crypto, hashing, packers conocidos. |
| Desofuscar | Simplifica lógica ofuscada. |

# Herramientas / integraciones

| Plugin | Entorno |
|--------|---------|
| **Gepetto** | IDA Pro (GPT). |
| **GhidraGPT / Ghidra+LLM** | Ghidra. |
| **aiDAPal**, **ReVa** | Asistentes RE. |
| MCP/locales | Modelos locales sobre el decompilado. |

# Límites

- **Alucinaciones**: el modelo inventa lógica plausible pero falsa → verificar siempre con debugging.
- Contexto limitado: binarios grandes no caben enteros.
- No fiar de conclusiones sobre maliciosidad sin análisis real.

> Útil para **acelerar el primer pase** y orientar; la verdad la da el desensamblado + ejecución. Combinar con [[Binary analysis]].

# Recursos
### [[Binary analysis]] · [[Buffer Overflow]]
### [Talos — LLM as a RE sidekick](https://blog.talosintelligence.com/using-llm-as-a-reverse-engineering-sidekick/)
