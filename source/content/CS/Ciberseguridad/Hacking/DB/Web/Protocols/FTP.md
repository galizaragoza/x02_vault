```
ftp <IP>
```

Mejor, más moderno
```zsh
lftp <IP>

login <user> <pass> ## Ya dentro
```

| `lcd`   | Change local directory.      | `lcd /path/to/directory` |
| ------- | ---------------------------- | ------------------------ |
| `cd`    | Change server directory.     | `cd /path/to/directory`  |
| `ls`    | List server directory files. | `ls`                     |
| `get`   | Download file from server.   | `get filename.txt`       |
| `mget`  | Download multiple files.     | `mget *.txt`             |
| `put`   | Upload file to server.       | `put filename.txt`       |
| `mput`  | Upload multiple files.       | `mput *.txt`             |
| `bin`   | Set binary transfer mode.    | `bin`                    |
| `ascii` | Set ASCII transfer mode.     | `ascii`                  |
| `quit`  | Exit FTP client.             | `quit`                   |

# Protips
### Download all files
```
wget -m ftp://anonymous:anonymous@X.X.X.X
```

### [FTP bounce attack](https://hackviser.com/tactics/pentesting/services/ftp#ftp-bounce-attack)
```sh
nmap -b <FTP_server>:<port> <target_network> ## Bouncing con nmap
```
