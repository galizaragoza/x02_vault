Bash predefines a set of **special parameters** (set by the shell, read-only in most cases) and **shell variables** (some read-only, some user-settable) that expose process state, positional arguments and runtime context.

# Special parameters

| Parameter | Description |
|-----------|-------------|
| `$*` | Expands to the positional parameters, starting from one. When quoted, `"$*"` joins them into a single word separated by the first char of `IFS`. |
| `$@` | Expands to the positional parameters, starting from one. `"$@"` is equivalent to `"$1" "$2" …` (each as a separate word). Prefer this for iterating arguments. |
| `$#` | Number of positional parameters in decimal. |
| `$?` | Exit status of the most recently executed foreground pipeline. |
| `$-` | Current option flags (set on invocation, by `set`, or by the shell, e.g. `i` for interactive). |
| `$$` | Process ID of the shell. In a subshell it expands to the PID of the invoking shell, not the subshell. |
| `$!` | Process ID of the job most recently placed into the background. |
| `$0` | Name of the shell or shell script. |
| `$_` | At startup, the absolute path of the shell/script; afterwards, the last argument of the previous command. Also holds the matching MAIL file when checking mail. |
| `$1`, `$2`, … `$9` | Positional parameters (script/function arguments). |
| `${10}`, `${11}`, … | Positional parameters above 9 require braces. |

# Process / shell identity

| Variable | Description |
|----------|-------------|
| `$BASHPID` | PID of the current Bash process (unlike `$$`, updates correctly inside subshells). |
| `$PPID` | PID of the shell's parent process. |
| `$UID` | Real user ID of the current user (read-only). |
| `$EUID` | Effective user ID (read-only). `0` when running as root. |
| `$GROUPS` | Array of groups the current user belongs to. |
| `$SHLVL` | Shell nesting level (incremented each time a new shell starts). |
| `$BASH` | Full pathname used to invoke this instance of Bash. |
| `$BASH_VERSION` | Version string of this Bash. |
| `$BASH_VERSINFO` | Array with the version components (major, minor, patch, …). |
| `$BASH_SUBSHELL` | Current subshell nesting depth (starts at 0). |
| `$HOSTNAME` | Name of the current host. |
| `$OSTYPE` | String describing the OS Bash runs on. |
| `$MACHTYPE` | System type in cpu-company-system format. |

# Runtime / execution context

| Variable | Description |
|----------|-------------|
| `$LINENO` | Current line number within the script or function. |
| `$FUNCNAME` | Array of function names in the call stack (`${FUNCNAME[0]}` = current function). |
| `$BASH_SOURCE` | Array of source filenames; `${BASH_SOURCE[0]}` is the file of the current function/script. |
| `$BASH_LINENO` | Array of line numbers corresponding to each entry in `BASH_SOURCE`. |
| `$BASH_COMMAND` | Command currently being executed (useful inside traps). |
| `$PIPESTATUS` | Array with the exit status of each command in the last foreground pipeline. |
| `$BASH_REMATCH` | Array with the matches from the last `[[ str =~ regex ]]` (`[0]` = whole match, `[1..]` = groups). |
| `$REPLY` | Default variable for `read` (when no name given) and for `select`. |
| `$SECONDS` | Seconds since the shell was started (settable). |
| `$RANDOM` | A pseudo-random integer 0–32767 each time it is read. |
| `$SRANDOM` | A 32-bit random integer from a high-quality source (Bash 5.1+). |
| `$EPOCHSECONDS` | Unix time in seconds (Bash 5+). |
| `$EPOCHREALTIME` | Unix time with microsecond precision (Bash 5+). |

# Environment / configuration (commonly used)

| Variable | Description |
|----------|-------------|
| `$PWD` | Current working directory. |
| `$OLDPWD` | Previous working directory (`cd -` returns here). |
| `$HOME` | Current user's home directory. |
| `$PATH` | Colon-separated list of directories searched for commands. |
| `$IFS` | Internal Field Separator: chars used for word splitting (default space, tab, newline). |
| `$PS1`–`$PS4` | Prompt strings: `PS1` primary, `PS2` continuation, `PS3` `select`, `PS4` `xtrace` prefix. |
| `$LANG` / `$LC_*` | Locale settings. |
| `$HISTFILE`, `$HISTSIZE` | History file path and in-memory size. |
| `$SHELL` | Path to the user's preferred shell (login shell), set by login. |
| `$LINES`, `$COLUMNS` | Terminal dimensions. |

> Read-only ones (`UID`, `EUID`, `BASHPID`, `PPID`, `BASH_VERSINFO`, …) cannot be assigned. `RANDOM`, `SECONDS`, `LINENO`, `IFS`, `PATH`, prompts, etc. can be set or overridden.
