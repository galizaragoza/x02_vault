atop is an interactive monitor for Linux system load, showing critical resources (CPU, memory, disk, network) on system and process levels. Supports interactive viewing, logging raw data for analysis, parseable output. Disk/network per process requires kernel modules.

| **Parameter** | **Function**                 | **Example**                       |
| --------- | ------------------------ | ----------------------------- |
| `-g`      | Generic process output.  | `atop -g`                     |
| `-m`      | Memory output.           | `atop -m`                     |
| `-d`      | Disk output.             | `atop -d`                     |
| `-n`      | Network output.          | `atop -n`                     |
| `-u`      | Per user activity.       | `atop -u`                     |
| `-p`      | Per program activity.    | `atop -p`                     |
| `-s`      | Scheduling info.         | `atop -s`                     |
| `-c`      | Command line.            | `atop -c`                     |
| `-v`      | Various process info.    | `atop -v`                     |
| `-y`      | Show threads.            | `atop -y`                     |
| `-C`      | Sort by CPU.             | `atop -C`                     |
| `-M`      | Sort by memory.          | `atop -M`                     |
| `-D`      | Sort by disk.            | `atop -D`                     |
| `-N`      | Sort by network.         | `atop -N`                     |
| `-A`      | Auto sort busy resource. | `atop -A`                     |
| `-w`      | Write raw file.          | `atop -w /tmp/atop.raw 600 6` |
| `-r`      | Read raw file.           | `atop -r /tmp/atop.raw`       |
| `-b`      | Begin time.              | `atop -r file -b 1400`        |
| `-e`      | End time.                | `atop -r file -e 1500`        |
| `-P`      | Parseable output.        | `atop -P CPU,DSK -r file`     |
| `-1`      | Average per second.      | `atop -1`                     |