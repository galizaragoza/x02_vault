`golint` es el linter de estilo histórico de Go: analiza código fuente y emite advertencias sobre desviaciones respecto a las convenciones de la guía oficial de estilo (nombres exportados sin comentario doc, receptores inconsistentes, naming `MixedCaps`, errores que no empiezan por minúscula, etc.). No comprueba errores de compilación ni bugs; solo estilo. **Está oficialmente obsoleto y archivado** (`golang/lint`): el propio proyecto recomienda migrar a [staticcheck](https://staticcheck.io), `revive` o `golangci-lint`. Se documenta aquí por su presencia en pipelines y proyectos legacy.

```
golint [flags] [paquetes | ficheros | directorios]
```

> Instalación legacy: `go install golang.org/x/lint/golint@latest`. A diferencia de `go vet`, `golint` no forma parte de la toolchain estándar.

---

## Flags

| Flag | Descripción | Ejemplo |
|------|-------------|---------|
| `-min_confidence FLOAT` | Confianza mínima (0.0–1.0) para reportar un problema. Por defecto `0.8`; bajarlo muestra más avisos. | `golint -min_confidence 0.3 ./...` |
| `-set_exit_status` | Devuelve código de salida `1` si hay hallazgos (útil en CI). Por defecto siempre sale `0`. | `golint -set_exit_status ./...` |
| `-h` / `-help` | Muestra el uso y las flags disponibles. | `golint -help` |

---

## Formas de invocación

| Destino | Descripción | Ejemplo |
|---------|-------------|---------|
| Sin args | Analiza el paquete del directorio actual. | `golint` |
| Ficheros | Lista de ficheros `.go` concretos. | `golint main.go util.go` |
| Directorio | Todos los `.go` del directorio. | `golint ./cmd` |
| Patrón recursivo | El comodín `./...` recorre todos los subpaquetes. | `golint ./...` |
| Import path | Un paquete por su ruta de importación. | `golint github.com/user/proj/pkg` |

---

## Casos de uso comunes

```bash
# Lintar todo el módulo de forma recursiva
golint ./...

# Modo CI: fallar el build si hay avisos
golint -set_exit_status ./...

# Ser más estricto (reportar también avisos de baja confianza)
golint -min_confidence 0.2 ./...

# Filtrar solo avisos de comentarios doc faltantes
golint ./... | grep "should have comment"
```

---

## Migración recomendada

`golint` está congelado. Equivalentes modernos:

| Herramienta | Rol |
|-------------|-----|
| `go vet` | Detección de errores sospechosos (parte de la toolchain). |
| `staticcheck` | Sucesor directo en análisis de estilo y bugs. |
| `revive` | Reescritura configurable y más rápida de golint. |
| `golangci-lint` | Meta-linter que agrega decenas de linters (incluye sustitutos de golint). |

Relacionado: [[go]], [[GoRoutines]].
