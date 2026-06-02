#reference

**String matching** = algoritmos para localizar un patrón dentro de un texto. Base de búsqueda, IDS/antivirus (firmas), grep y análisis forense. Relevante en seguridad por el coste y por ataques de tipo **ReDoS**.

# Algoritmos clásicos

| Algoritmo | Idea | Complejidad |
|-----------|------|-------------|
| **Naïve** | Comparar el patrón en cada posición. | O(n·m) |
| **KMP** (Knuth-Morris-Pratt) | Tabla de prefijos para no retroceder en el texto. | O(n+m) |
| **Boyer-Moore** | Compara de derecha a izquierda; saltos grandes (bad char / good suffix). | sublineal en la práctica |
| **Rabin-Karp** | Hashing rodante; ideal para múltiples patrones. | O(n+m) medio |
| **Aho-Corasick** | Autómata para buscar **muchos patrones a la vez**. | O(n + Σm + coincidencias) |

> **Aho-Corasick** es el motor típico de IDS/AV (Snort, ClamAV): buscar miles de firmas en un solo paso sobre el tráfico.

# Búsqueda aproximada / regex

- **Levenshtein / distancia de edición**: coincidencia difusa (fuzzy), corrección de typos.
- **Regex**: motores NFA pueden sufrir **backtracking catastrófico**.

# Seguridad: ReDoS

Regex mal diseñadas (`(a+)+$`) con backtracking exponencial → un input pequeño cuelga la CPU = **DoS**.

```
Patrón vulnerable:  ^(a+)+$
Input:              "aaaaaaaaaaaaaaaaaaaaaaaa!"   -> explosión de backtracking
```

Mitigación: motores lineales (RE2), límites de tiempo/longitud, evitar grupos anidados cuantificados.

# Recursos
### [Wikipedia — String-searching algorithm](https://en.wikipedia.org/wiki/String-searching_algorithm)
### [Aho-Corasick](https://en.wikipedia.org/wiki/Aho%E2%80%93Corasick_algorithm) · [OWASP — ReDoS](https://owasp.org/www-community/attacks/Regular_expression_Denial_of_Service_-_ReDoS)
