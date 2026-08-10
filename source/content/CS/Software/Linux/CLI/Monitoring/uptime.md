`uptime` shows, on a single line, the current time, how long the system has been running, the number of logged-in users, and the system load averages over the last 1, 5 and 15 minutes. It reads `/proc/uptime` and `/proc/loadavg`, so it has no measurable overhead. It ships as part of procps-ng. The load average is the average number of runnable/uninterruptible processes; on an N-core machine, values near N indicate full utilization.

```
uptime [options]
```

# Output format

| **Option** | **Function** | **Example** |
| --- | --- | --- |
| `-p, --pretty` | Human-readable uptime ("up 3 hours, 12 minutes"). | `uptime -p` |
| `-s, --since` | Print the time the system booted (yyyy-mm-dd HH:MM:SS). | `uptime -s` |

# Help and version

| **Option** | **Function** | **Example** |
| --- | --- | --- |
| `-h, --help` | Display usage help and exit. | `uptime -h` |
| `-V, --version` | Display version information and exit. | `uptime -V` |

## Notes

- Run without options, the default line is equivalent to the header of [[top]] and [[w]].
- Load averages above the core count over the 5/15-min windows signal sustained saturation; correlate with [[vmstat]] / [[sar]] to find the cause.
