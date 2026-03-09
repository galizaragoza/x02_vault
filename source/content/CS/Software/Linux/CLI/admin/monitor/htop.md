htop is an ncurses-based interactive process viewer, allowing scrolling, mouse interaction, tree view, multi-process selection, kill/renice without PIDs.

| **Parameter**    | **Function**                         | **Example**                   |
| ------------ | -------------------------------- | ------------------------- |
| `-d`         | Update delay (tenths of second). | `htop -d 5`               |
| `-C`         | Monochrome mode.                 | `htop -C`                 |
| `-F`         | Filter processes.                | `htop -F "nginx\|apache"` |
| `-h`         | Help.                            | `htop -h`                 |
| `-p`         | Show PIDs.                       | `htop -p 1234,5678`       |
| `-s`         | Sort by column.                  | `htop -s cpu`             |
| `-u`         | User processes.                  | `htop -u john`            |
| `-U`         | ASCII meters.                    | `htop -U`                 |
| `-M`         | Disable mouse.                   | `htop -M`                 |
| `--readonly` | Disable changes.                 | `htop --readonly`         |
| `-V`         | Version.                         | `htop -V`                 |
| `-t`         | Tree view.                       | `htop -t`                 |
| `-H`         | Highlight changes.               | `htop -H 2`               |