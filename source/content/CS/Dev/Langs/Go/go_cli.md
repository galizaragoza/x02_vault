| **Utilidad** | **Función**                                                     | **Ejemplo de Sintaxis**           |
| ------------ | --------------------------------------------------------------- | --------------------------------- |
| `mod init`   | Inicializa un nuevo módulo (gestión de dependencias).           | `go mod init mi-proyecto`         |
| `run`        | Compila y ejecuta un archivo fuente temporalmente.              | `go run main.go`                  |
| `build`      | Compila los paquetes y dependencias en un binario ejecutable.   | `go build -o mi-programa`         |
| `get`        | Añade dependencias externas al módulo actual.                   | `go get github.com/gin-gonic/gin` |
| `test`       | Ejecuta los archivos de prueba (`_test.go`) del proyecto.       | `go test ./...`                   |
| `fmt`        | Formatea automáticamente el código siguiendo el estándar de Go. | `go fmt ./...`                    |
| `install`    | Compila e instala el binario en `$GOPATH/bin`.                  | `go install`                      |
| `version`    | Imprime la versión instalada del entorno Go.                    | `go version`                      |
