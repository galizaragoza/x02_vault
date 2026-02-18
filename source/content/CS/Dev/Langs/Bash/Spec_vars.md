
| `$*` | Expands to the positional parameters, starting from one.                                                                                                  |
| ---- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `$@` | Expands to the positional parameters, starting from one. That is, `"$@"` is equivalent to `"$1" "$2" …`.                                                  |
| `$#` | Expands to the number of positional parameters in decimal.                                                                                                |
| `$?` | Expands to the exit status of the most recently executed foreground pipeline.                                                                             |
| `$-` | Expands to the current option flags as specified upon invocation, by the `set` builtin command, or those set by the shell itself (such as the -i option). |
| `$$` | Expands to the process ID of the shell. In a subshell, it expands to the process ID of the invoking shell, not the subshell.                              |
| `$!` | Expands to the process ID of the job most recently placed into the background                                                                             |
| `$0` | Expands to the name of the shell or shell script                                                                                                          |
