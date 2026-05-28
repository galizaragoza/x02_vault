`truncate` es una utilidad de línea de comandos de Unix/Linux que se utiliza para reducir o ampliar el tamaño de un archivo a un tamaño específico. A diferencia de un editor de texto, no manipula el contenido de forma inteligente; simplemente corta el archivo o lo rellena con bytes nulos (huecos) para alcanzar la dimensión deseada.

| **Parámetro**       | **Función**                                                                        | **Ejemplo de Sintaxis**         |
| ------------------- | ---------------------------------------------------------------------------------- | ------------------------------- |
| `-s`, `--size`      | Define el tamaño final del archivo. Permite sufijos como `K`, `M`, `G`.            | `truncate -s 10M archivo.log`   |
| `-c`, `--no-create` | No crea el archivo si este no existe previamente.                                  | `truncate -c -s 0 temporal.txt` |
| `-o`, `--io-blocks` | Trata el número indicado en `-s` como bloques de entrada/salida en lugar de bytes. | `truncate -o -s 5 archivo.bin`  |
| `+` (en size)       | Incrementa el tamaño actual del archivo en la cantidad indicada.                   | `truncate -s +1K archivo.txt`   |
| `-` (en size)       | Reduce el tamaño actual del archivo en la cantidad indicada.                       | `truncate -s -500M archivo.iso` |