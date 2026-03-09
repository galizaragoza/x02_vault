ltrace traces dynamic library calls and signals of processes, similar to strace but for libraries; displays parameters using prototypes.

| **Parameter** | **Function**             | **Example**                                       |
| --------- | -------------------- | --------------------------------------------- |
| `-a`      | Align returns.       | `ltrace -a 40 ./program`                      |
| `-A`      | Array elements.      | `ltrace -A 5 ./program`                       |
| `-b`      | Disable signals.     | `ltrace -b ./program`                         |
| `-c`      | Count summary.       | `ltrace -c ./program`                         |
| `-C`      | Demangle names.      | `ltrace -C ./program`                         |
| `-e`      | Trace expression.    | `ltrace -e 'malloc+free-@libc.so*' ./program` |
| `-f`      | Follow children.     | `ltrace -f ./program`                         |
| `-i`      | Instruction pointer. | `ltrace -i ./program`                         |
| `-l`      | Library pattern.     | `ltrace -l libc.so.6 ./program`               |
| `-n`      | Indent nesting.      | `ltrace -n 4 ./program`                       |
| `-o`      | Output file.         | `ltrace -o output.txt ./program`              |
| `-p`      | Attach PID.          | `ltrace -p 1234`                              |
| `-r`      | Relative timestamp.  | `ltrace -r ./program`                         |
| `-s`      | String size.         | `ltrace -s 64 ./program`                      |
| `-S`      | System calls.        | `ltrace -S ./program`                         |
| `-t`      | Time prefix.         | `ltrace -t ./program`                         |
| `-T`      | Time in call.        | `ltrace -T ./program`                         |
| `-u`      | Run as user.         | `ltrace -u user ./program`                    |
| `-x`      | Symbol points.       | `ltrace -x 'func*' ./program`                 |