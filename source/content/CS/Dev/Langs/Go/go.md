`go` es la herramienta principal de la toolchain de Go: compila, ejecuta, prueba, formatea y gestiona dependencias de módulos, además de envolver herramientas auxiliares (`vet`, `cover`, `pprof`...). Funciona sobre **módulos** (`go.mod`/`go.sum`) y entiende patrones de paquete como `./...` (todo el árbol) o rutas de importación. La sintaxis general es `go <comando> [argumentos]`; la mayoría de comandos aceptan flags de compilación comunes.

```
go <comando> [flags] [paquetes]
```

> Patrones de paquete habituales: `.` (paquete actual), `./...` (recursivo desde aquí), `all` (todos los del módulo + deps), o una ruta de importación (`github.com/user/proj/pkg`). Variables clave: `GOPATH`, `GOROOT`, `GOBIN`, `GOOS`, `GOARCH`, `GOPROXY`, `GOFLAGS`, `CGO_ENABLED`.

---

## Comandos principales

| Comando        | Descripción                                             | Ejemplo                                  |
| -------------- | ------------------------------------------------------- | ---------------------------------------- |
| `go run`       | Compila y ejecuta (binario temporal, no lo guarda).     | `go run main.go`                         |
| `go build`     | Compila paquetes y deps a un binario.                   | `go build -o app ./cmd`                  |
| `go install`   | Compila e instala el binario en `$GOBIN`/`$GOPATH/bin`. | `go install ./cmd/app`                   |
| `go test`      | Ejecuta tests (`_test.go`) y benchmarks.                | `go test ./...`                          |
| `go get`       | Añade/actualiza dependencias del módulo.                | `go get github.com/gin-gonic/gin@latest` |
| `go fmt`       | Formatea código (envoltorio de `gofmt -l -w`).          | `go fmt ./...`                           |
| `go vet`       | Análisis estático de construcciones sospechosas.        | `go vet ./...`                           |
| `go generate`  | Ejecuta directivas `//go:generate` del código.          | `go generate ./...`                      |
| `go doc`       | Muestra documentación de un paquete/símbolo.            | `go doc fmt.Println`                     |
| `go clean`     | Elimina objetos y cachés de build.                      | `go clean -cache`                        |
| `go list`      | Lista paquetes/módulos y sus metadatos.                 | `go list -m all`                         |
| `go env`       | Muestra/edita la configuración del entorno Go.          | `go env GOPATH`                          |
| `go version`   | Imprime la versión de Go (o de un binario).             | `go version`                             |
| `go fix`       | Actualiza código a APIs nuevas.                         | `go fix ./...`                           |
| `go bug`       | Abre un reporte de bug con info del sistema.            | `go bug`                                 |
| `go work`      | Gestiona workspaces multi-módulo (Go 1.18+).            | `go work init ./a ./b`                   |
| `go mod`       | Gestión de módulos (ver sección).                       | `go mod tidy`                            |
| `go tool`      | Invoca herramientas internas (`pprof`, `cover`...).     | `go tool pprof cpu.prof`                 |
| `go telemetry` | Configura la telemetría de la toolchain (Go 1.23+).     | `go telemetry off`                       |

---

## Flags de compilación comunes

Aplican a `build`, `install`, `run`, `test`, `vet`.

| Flag               | Descripción                                               | Ejemplo                                     |
| ------------------ | --------------------------------------------------------- | ------------------------------------------- |
| `-o FICHERO`       | Nombre/ruta del binario de salida.                        | `go build -o bin/app`                       |
| `-v`               | Lista los paquetes a medida que se compilan.              | `go build -v ./...`                         |
| `-x`               | Muestra los comandos ejecutados.                          | `go build -x`                               |
| `-n`               | Imprime los comandos sin ejecutarlos.                     | `go build -n`                               |
| `-race`            | Activa el detector de data races.                         | `go test -race ./...`                       |
| `-msan` / `-asan`  | Memory/Address Sanitizer (requiere clang/CGO).            | `go build -msan ./...`                      |
| `-tags 'LISTA'`    | Build tags para compilación condicional.                  | `go build -tags 'prod netgo'`               |
| `-ldflags 'FLAGS'` | Pasa flags al linker (p. ej. inyectar versión).           | `go build -ldflags="-s -w -X main.ver=1.2"` |
| `-gcflags 'FLAGS'` | Flags al compilador (p. ej. `-m` para inlining/escape).   | `go build -gcflags='-m'`                    |
| `-trimpath`        | Quita rutas absolutas del binario (builds reproducibles). | `go build -trimpath`                        |
| `-mod=MODO`        | Modo de módulos: `mod`, `readonly`, `vendor`.             | `go build -mod=vendor`                      |
| `-p N`             | Nº de compilaciones en paralelo.                          | `go build -p 4`                             |
| `-a`               | Fuerza recompilar todo, ignorando caché.                  | `go build -a ./...`                         |
| `-work`            | Conserva y muestra el directorio temporal de build.       | `go build -work`                            |
| `-buildmode=MODO`  | Tipo de salida: `exe`, `c-shared`, `pie`, `plugin`...     | `go build -buildmode=c-shared`              |
| `-cover`           | Instrumenta para cobertura (también en build, Go 1.20+).  | `go build -cover -o app`                    |

> Cross-compilación: anteponer `GOOS`/`GOARCH`. Ej.: `GOOS=linux GOARCH=arm64 go build -o app-arm64`. Binario estático: `CGO_ENABLED=0 go build`.

---

## go run / build / install

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `go run .` | Compila y ejecuta el paquete del directorio. | `go run .` |
| `go run pkg@version` | Ejecuta una herramienta sin instalarla (Go 1.22+). | `go run golang.org/x/tools/cmd/stringer@latest` |
| `go run main.go arg1` | Pasa argumentos tras los ficheros. | `go run main.go --flag x` |
| `go build ./...` | Compila todo el árbol (no genera binarios de libs). | `go build ./...` |
| `go install pkg@version` | Instala una CLI desde su ruta a `$GOBIN`. | `go install github.com/x/tool@v1.4.0` |

---

## go test — pruebas, benchmarks y cobertura

| Flag | Descripción | Ejemplo |
|------|-------------|---------|
| `-run REGEX` | Ejecuta solo los tests que casen el patrón. | `go test -run TestLogin ./...` |
| `-bench REGEX` | Ejecuta benchmarks que casen (`.` = todos). | `go test -bench=. ./...` |
| `-benchmem` | Reporta asignaciones de memoria en benchmarks. | `go test -bench=. -benchmem` |
| `-benchtime DUR/N` | Duración o nº de iteraciones por benchmark. | `go test -bench=. -benchtime=5s` |
| `-v` | Salida verbosa (nombre y estado de cada test). | `go test -v ./...` |
| `-count N` | Repite los tests N veces (`-count=1` evita la caché). | `go test -count=1 ./...` |
| `-race` | Detector de data races durante los tests. | `go test -race ./...` |
| `-cover` | Reporta porcentaje de cobertura. | `go test -cover ./...` |
| `-coverprofile FICH` | Guarda el perfil de cobertura. | `go test -coverprofile=cov.out ./...` |
| `-coverpkg LISTA` | Mide cobertura de paquetes dados. | `go test -coverpkg=./... ./...` |
| `-timeout DUR` | Timeout global de la ejecución. | `go test -timeout 30s` |
| `-short` | Activa el modo corto (los tests deben respetar `testing.Short()`). | `go test -short ./...` |
| `-failfast` | Para al primer fallo. | `go test -failfast ./...` |
| `-parallel N` | Máximo de tests `t.Parallel()` simultáneos. | `go test -parallel 8` |
| `-cpu LISTA` | Ejecuta con distintos `GOMAXPROCS`. | `go test -cpu=1,2,4` |
| `-json` | Salida en JSON (para herramientas/CI). | `go test -json ./...` |

```bash
# Ver cobertura en HTML
go test -coverprofile=cov.out ./... && go tool cover -html=cov.out
```

---

## go mod — gestión de módulos

| Subcomando | Descripción | Ejemplo |
|------------|-------------|---------|
| `go mod init [ruta]` | Crea `go.mod` con la ruta del módulo. | `go mod init github.com/me/proj` |
| `go mod tidy` | Añade deps faltantes y elimina las no usadas. | `go mod tidy` |
| `go mod download` | Descarga deps a la caché de módulos. | `go mod download` |
| `go mod verify` | Verifica que las deps de la caché no han sido alteradas. | `go mod verify` |
| `go mod graph` | Imprime el grafo de dependencias. | `go mod graph` |
| `go mod why PKG` | Explica por qué se necesita un paquete/módulo. | `go mod why github.com/x/y` |
| `go mod edit FLAGS` | Edita `go.mod` mediante flags (scriptable). | `go mod edit -require=x@v1.2.0` |
| `go mod vendor` | Copia las deps a `./vendor`. | `go mod vendor` |

> `go.sum` guarda los hashes criptográficos de cada dependencia para integridad. `GOFLAGS=-mod=mod` permite que la build modifique `go.mod`.

---

## go get — dependencias

| Forma | Descripción | Ejemplo |
|-------|-------------|---------|
| `go get pkg@latest` | Última versión etiquetada. | `go get github.com/x/y@latest` |
| `go get pkg@v1.2.3` | Versión concreta. | `go get github.com/x/y@v1.2.3` |
| `go get pkg@commit` | Pseudo-versión desde un commit/branch. | `go get github.com/x/y@e3702be` |
| `go get pkg@none` | Elimina la dependencia. | `go get github.com/x/y@none` |
| `go get -u` | Actualiza deps a versiones minor/patch más recientes. | `go get -u ./...` |
| `go get -u=patch` | Actualiza solo a parches. | `go get -u=patch ./...` |
| `go get -t` | Incluye también deps de los tests. | `go get -t ./...` |

> Desde Go 1.17, `go get` ya **no** instala binarios; para eso usa `go install pkg@version`.

---

## go work — workspaces multi-módulo

| Subcomando | Descripción | Ejemplo |
|------------|-------------|---------|
| `go work init [dirs]` | Crea `go.work` con los módulos dados. | `go work init ./api ./lib` |
| `go work use DIR` | Añade un módulo al workspace. | `go work use ./newmod` |
| `go work sync` | Propaga las versiones del workspace a los `go.mod`. | `go work sync` |
| `go work edit FLAGS` | Edita `go.work` por flags. | `go work edit -use=./extra` |

---

## go tool — herramientas internas

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `go tool` | Lista las herramientas disponibles. | `go tool` |
| `go tool pprof` | Análisis de perfiles CPU/heap. | `go tool pprof cpu.prof` |
| `go tool cover` | Procesa perfiles de cobertura. | `go tool cover -html=cov.out` |
| `go tool trace` | Visualiza trazas de ejecución. | `go tool trace trace.out` |
| `go tool objdump` | Desensambla un binario/paquete. | `go tool objdump -s main.main app` |
| `go tool nm` | Lista símbolos de un objeto/binario. | `go tool nm app` |
| `go tool dist list` | Lista todas las combinaciones `GOOS/GOARCH`. | `go tool dist list` |

---

## go env — configuración del entorno

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `go env` | Muestra todas las variables. | `go env` |
| `go env VAR` | Muestra una variable. | `go env GOPATH` |
| `go env -json` | Salida en JSON. | `go env -json` |
| `go env -w VAR=VAL` | Fija una variable de forma persistente (en `go/env`). | `go env -w GOPROXY=direct` |
| `go env -u VAR` | Borra el valor persistente fijado. | `go env -u GOPROXY` |

| Variable | Rol |
|----------|-----|
| `GOOS` / `GOARCH` | SO / arquitectura destino (cross-compile). |
| `CGO_ENABLED` | `0` desactiva CGO → binario estático puro Go. |
| `GOPROXY` | Proxy de módulos (`direct` = sin proxy). |
| `GOPRIVATE` | Patrones de módulos privados (saltan proxy/checksum). |
| `GOFLAGS` | Flags aplicadas a todos los comandos `go`. |
| `GOBIN` | Destino de `go install`. |
| `GOMODCACHE` | Caché de módulos descargados. |

---

## Casos de uso comunes

```bash
# Iniciar proyecto
go mod init github.com/me/app
go mod tidy

# Build de release: stripped, reproducible, con versión inyectada
CGO_ENABLED=0 go build -trimpath \
  -ldflags="-s -w -X main.version=$(git describe --tags)" -o app ./cmd/app

# Cross-compile a Linux ARM64
GOOS=linux GOARCH=arm64 go build -o app-arm64 ./cmd/app

# Tests con race + cobertura sin caché
go test -race -count=1 -coverprofile=cov.out ./...
go tool cover -html=cov.out

# Benchmarks con memoria
go test -bench=. -benchmem ./...

# Ejecutar una CLI puntual sin instalarla
go run golang.org/x/tools/cmd/goimports@latest -w .

# Actualizar todas las deps a últimos parches
go get -u=patch ./... && go mod tidy

# Perfilado de CPU
go test -cpuprofile=cpu.prof -bench=. ./pkg
go tool pprof -http=:8080 cpu.prof
```

Relacionado: [[golint]], [[GoRoutines]], [[clang]], [[make]].
