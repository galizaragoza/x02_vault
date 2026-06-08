#reference #IA

**Embeddings** = representación de datos (palabras, frases, imágenes) como **vectores densos** en un espacio de muchas dimensiones, donde la **distancia/dirección** captura significado. Items semánticamente parecidos quedan cerca. Base de LLMs, búsqueda semántica y RAG.

# Idea

```
"rey" - "hombre" + "mujer" ≈ "reina"     # las relaciones son vectoriales
similitud(v1, v2) = coseno(v1, v2)        # cercano a 1 -> muy parecido
```

| Concepto | Significado |
|----------|-------------|
| **Dimensión** | Tamaño del vector (p.ej. 768, 1536). |
| **Similitud coseno** | Métrica habitual de cercanía semántica. |
| **Espacio latente** | Espacio donde viven los vectores. |

# En un LLM (Transformer)

1. **Token embeddings**: cada token → vector aprendido.
2. **Positional encoding**: se añade información de posición (los Transformers no tienen orden inherente). Variante moderna: **RoPE** (Rotary Position Embedding) → codifica posición rotando el vector, mejor extrapolación a contextos largos.
3. Las capas de atención transforman esos vectores en representaciones contextuales.

# Usos

| Uso | Cómo |
|-----|------|
| **Búsqueda semántica** | Buscar por significado, no por palabras exactas. |
| **RAG** | Recuperar documentos relevantes por similitud para alimentar al LLM. |
| **Clustering / clasificación** | Agrupar por cercanía vectorial. |
| **Recomendación** | Items cercanos al perfil del usuario. |
| **Multimodal** | Texto e imágenes en el mismo espacio (CLIP). |

# Vector databases

Almacenan embeddings e indexan para **búsqueda por vecinos más cercanos (ANN)**: FAISS, Pinecone, Qdrant, Chroma, pgvector.

```python
from sentence_transformers import SentenceTransformer
m = SentenceTransformer("all-MiniLM-L6-v2")
v = m.encode(["texto de ejemplo"])     # -> vector de 384 dims
```

# Recursos
### [[contexto-y-rag]]
### [Multimodal embeddings (TDS)](https://towardsdatascience.com/multimodal-embeddings-an-introduction-5dc36975966f/) · [RoPE explained](https://towardsdatascience.com/rope-clearly-explained/)
