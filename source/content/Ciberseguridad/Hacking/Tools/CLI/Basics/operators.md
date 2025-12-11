
| **Operator**  | **Function**                                                               | **Example**           |
| --------- | ---------------------------------------------------------------------- | ----------------- |
| `>`       | Redirige el stdout del comando al archivo escogido (sobreescribe)      | `command > file`  |
| `>>`      | Redirige el stdout del comando al archivo escogido (lo añade al final) | `command >> file` |
| \| (pipe) | Envía el stdout del comando 1 al stdin del comando 2 (stackeable)      | cmd1 \| cmd2<br>  |
| `2>`      | Redirige el stderror al archivo escogido                               | `cmd > a 2> b`    |
| `2>&1`    | Redirige el stderror al mismo lugar que el stdout                      | `cmd > a 2>&1`    |
| `<`       | Redirige el stdin                                                      | `cmd < file`      |
# CLI shortkeys
| **Keystroke** | **Action**                                        |
| ------------- | ------------------------------------------------- |
| CTRL-B        | Move the cursor left                              |
| CTRL-F        | Move the cursor right                             |
| CTRL-P        | View the previous command (or move the cursor up) |
| CTRL-N        | View the next command (or move the cursor down)   |
| CTRL-A        | Move the cursor to the beginning of the line      |
| CTRL-E        | Move the cursor to the end of the line            |
| CTRL-W        | Erase the preceding word                          |
| CTRL-U        | Erase from cursor to beginning of line            |
| CTRL-K        | Erase from cursor to end of line                  |
| CTRL-Y        | Paste erased text (for example, from CTRL-U)      |
| CTRL-L        | Clear the screen                                  |
| CTRL-R        | Reverse-incremental search in command history     |
