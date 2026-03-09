lsof lists open files by processes, including regular files, directories, devices, sockets, streams; supports selection by path, formatted/parsed output, repeat mode.

| **Parameter** | **Function**             | **Example**                        |
| --------- | -------------------- | ------------------------------ |
| `-c`      | Count summary.       | `strace -c command`            |
| `-e`      | Trace expression.    | `strace -e trace=open command` |
| `-f`      | Follow forks.        | `strace -f command`            |
| `-i`      | Instruction pointer. | `strace -i command`            |
| `-o`      | Output file.         | `strace -o output.txt command` |
| `-p`      | Attach PID.          | `strace -p 1234`               |
| `-q`      | Suppress messages.   | `strace -qqq command`          |
| `-r`      | Relative timestamps. | `strace -r command`            |
| `-s`      | String size.         | `strace -s 64 command`         |
| `-t`      | Absolute time.       | `strace -t command`            |
| `-T`      | Time in call.        | `strace -T command`            |
| `-v`      | Unabbreviated.       | `strace -v command`            |
| `-x`      | Hex strings.         | `strace -x command`            |
| `-yy`     | FD info.             | `strace -yy command`           |