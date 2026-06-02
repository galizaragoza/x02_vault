#reference #Criptografia

**SSE** (Searchable Symmetric Encryption) = esquema que permite **buscar palabras clave sobre datos cifrados** sin descifrarlos. El servidor almacena el cifrado y responde consultas sin conocer el contenido ni (idealmente) la consulta.

# Problema que resuelve

Subir datos a un servidor no confiable (cloud) cifrados, pero poder **buscar** en ellos. Cifrado normal lo impide: para buscar habría que descargar y descifrar todo.

# Funcionamiento

| Fase | Qué ocurre |
|------|-----------|
| **Setup** | El cliente cifra los documentos y construye un **índice cifrado** (encrypted index) que mapea trapdoors → documentos. |
| **Trapdoor** | Para buscar "factura", el cliente genera un *token* determinista de esa palabra con su clave. |
| **Search** | El servidor usa el token contra el índice y devuelve los IDs de documentos coincidentes, sin aprender la palabra. |

# Compromisos (leakage)

SSE no es perfecto: filtra metadatos para ser eficiente.

| Tipo de fuga | Descripción |
|--------------|-------------|
| **Search pattern** | El servidor ve si dos consultas son la misma palabra (tokens deterministas). |
| **Access pattern** | Ve qué documentos devuelve cada consulta → ataques de inferencia/conteo. |
| **Volume** | El nº de resultados puede revelar la palabra (count attacks). |

> **Dynamic SSE** soporta añadir/borrar documentos; debe ofrecer *forward privacy* (una inserción nueva no se asocia a consultas pasadas) y *backward privacy* (lo borrado no aparece en consultas futuras).

Alternativas/relacionados: **ORAM** (oculta el access pattern, más costoso), **cifrado homomórfico**, **PIR** (Private Information Retrieval).

# Recursos
### [Wikipedia — Searchable symmetric encryption](https://en.wikipedia.org/wiki/Searchable_symmetric_encryption)
