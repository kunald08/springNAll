# Shell Scripting — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. Answers are written exactly how you should speak in an interview — clear, confident, plain English.

---

## 1. What is Shell Scripting? Why do we use it?

**Answer:**
A shell script is a text file containing a sequence of Linux commands that runs as a program. Instead of typing commands one by one, I write them in a script and execute the script.

We use shell scripting for **automation** — repetitive tasks like backups, log rotation, server health checks, deployments, and environment setup. Anything I do manually more than twice, I automate with a script.

The most common shell is **Bash** (Bourne Again Shell), which is the default on most Linux distributions. The script starts with a **shebang** line: `#!/bin/bash` — this tells the system which interpreter to use.

---

## 2. How do you create and run a shell script?

**Answer:**
Three steps:

1. **Create the file:** `vi script.sh` or `nano script.sh`
2. **Add the shebang and commands:**
```bash
#!/bin/bash
echo "Hello, World!"
```
3. **Make it executable and run:**
```bash
chmod +x script.sh
./script.sh
```

Alternatively, I can run it without making it executable: `bash script.sh`. But the `chmod +x` approach is cleaner and the standard practice.

---

## 3. What are variables in shell scripting? How do you declare them?

**Answer:**
Variables store data. In Bash, there's **no data type declaration** — everything is treated as a string unless used in arithmetic.

```bash
# Declaration (NO spaces around =)
name="Kunal"
age=25
path="/home/kunal"

# Accessing (use $ prefix)
echo $name
echo "Hello, ${name}!"   # Curly braces for clarity

# Read-only variable
readonly DB_HOST="localhost"

# Environment variable (available to child processes)
export API_KEY="abc123"
```

Key rules: no spaces around `=`, use `$` to access, use double quotes to handle spaces in values, and use `${var}` inside strings to avoid ambiguity.

---

## 4. What is the difference between single quotes and double quotes?

**Answer:**
- **Double quotes (`"`)** — allow variable expansion and command substitution. `echo "Hello $name"` prints "Hello Kunal".
- **Single quotes (`'`)** — treat everything literally. `echo 'Hello $name'` prints "Hello $name" — it doesn't expand the variable.
- **Backticks (`` ` ``) or `$()`** — command substitution. `echo "Today is $(date)"` runs the `date` command and inserts the result.

My rule: use double quotes for most things, single quotes when I want literal strings (like regex patterns or things that shouldn't be expanded).

---

## 5. What are special variables in Bash?

**Answer:**
These are built-in read-only variables:

- `$0` — the script's own name
- `$1, $2, ... $9` — positional parameters (command-line arguments)
- `$#` — total number of arguments passed
- `$@` — all arguments as separate words (preferred for iteration)
- `$*` — all arguments as a single string
- `$?` — exit status of the last command (0 = success, non-zero = failure)
- `$$` — process ID of the current script
- `$!` — process ID of the last background command

The most important one is **`$?`** — I use it constantly to check if the previous command succeeded before proceeding.

---

## 6. How does `if-else` work in shell scripting?

**Answer:**
```bash
if [ condition ]; then
    # commands
elif [ condition ]; then
    # commands
else
    # commands
fi
```

Important syntax details:
- **Spaces are mandatory** inside `[ ]` — `[ $age -gt 18 ]` NOT `[$age -gt 18]`
- Use `-eq, -ne, -gt, -lt, -ge, -le` for number comparison
- Use `=` and `!=` for string comparison
- Use `-z` (empty) and `-n` (not empty) for string checks
- Use `-f` (file exists), `-d` (directory exists), `-r` (readable), `-w` (writable)

For more advanced conditions, I use **double brackets `[[ ]]`** which support `&&`, `||`, regex matching with `=~`, and pattern matching. They're more forgiving with quoting too.

```bash
if [[ $status == "active" && $count -gt 0 ]]; then
    echo "All good"
fi
```

---

## 7. Explain the different types of loops in shell scripting.

**Answer:**
**For loop:**
```bash
# Iterate over a list
for item in apple banana cherry; do
    echo "$item"
done

# C-style for loop
for ((i=1; i<=5; i++)); do
    echo "$i"
done

# Iterate over files
for file in *.log; do
    echo "Processing $file"
done
```

**While loop:**
```bash
count=1
while [ $count -le 5 ]; do
    echo "$count"
    ((count++))
done

# Read file line by line
while IFS= read -r line; do
    echo "$line"
done < file.txt
```

**Until loop** — runs until the condition becomes TRUE (opposite of while):
```bash
count=1
until [ $count -gt 5 ]; do
    echo "$count"
    ((count++))
done
```

`break` exits the loop, `continue` skips to the next iteration.

---

## 8. How do you handle command-line arguments in a script?

**Answer:**
Arguments are accessed positionally with `$1`, `$2`, etc. For more robust handling, I use **`getopts`**:

```bash
#!/bin/bash

# Simple approach
echo "Script: $0"
echo "First arg: $1"
echo "All args: $@"
echo "Count: $#"

# Robust approach with getopts
while getopts "u:p:h" opt; do
    case $opt in
        u) username="$OPTARG" ;;
        p) password="$OPTARG" ;;
        h) echo "Usage: $0 -u username -p password"; exit 0 ;;
        *) echo "Invalid option"; exit 1 ;;
    esac
done

echo "User: $username"
```

Usage: `./script.sh -u kunal -p secret`

I always validate arguments at the beginning: check if enough arguments were provided, check for required flags, and show a usage message if something is missing.

---

## 9. How do you write functions in shell scripts?

**Answer:**
```bash
# Define a function
greet() {
    local name=$1    # 'local' keeps variable scoped to function
    echo "Hello, $name!"
}

# Call the function
greet "Kunal"

# Function with return value (exit code)
is_file_exists() {
    if [ -f "$1" ]; then
        return 0   # success (true)
    else
        return 1   # failure (false)
    fi
}

# Check the return
if is_file_exists "/etc/hosts"; then
    echo "File exists"
fi

# For string/complex return values, use echo + command substitution
get_date() {
    echo "$(date +%Y-%m-%d)"
}
today=$(get_date)
```

Key points: functions receive arguments as `$1, $2` (like scripts), use `local` for local variables, and `return` only returns exit codes (0-255). For actual data, use `echo` and capture with `$()`.

---

## 10. What is the difference between `$@` and `$*`?

**Answer:**
Both represent all command-line arguments, but they behave differently when quoted:

- **`"$@"`** — keeps each argument as a separate word. If I pass `"arg 1" "arg 2"`, it stays as two arguments.
- **`"$*"`** — joins all arguments into a single string. `"arg 1" "arg 2"` becomes one string `"arg 1 arg 2"`.

In practice, I almost always use **`"$@"`** because it preserves the original argument boundaries. This matters when filenames or arguments contain spaces.

```bash
# Correct: preserves arguments with spaces
for arg in "$@"; do
    echo "Argument: $arg"
done
```

---

## 11. What is `set -euo pipefail` and why should you use it?

**Answer:**
It's a best practice to put this at the top of every production script. It enables **strict mode**:

- **`set -e`** — exit immediately if any command fails (non-zero exit code). Without this, the script continues even after errors, which can cause silent damage.
- **`set -u`** — treat unset variables as an error. Prevents bugs from typos in variable names.
- **`set -o pipefail`** — if any command in a pipe fails, the pipe's exit code is the failing command's code (not just the last command's).

Without these, Bash is **dangerously permissive** — it ignores errors, undefined variables, and pipe failures by default.

```bash
#!/bin/bash
set -euo pipefail
```

---

## 12. How do you handle errors and traps in shell scripts?

**Answer:**
**Checking exit codes:**
```bash
if ! command; then
    echo "Command failed"
    exit 1
fi
```

**Using `trap`** — run cleanup code when the script exits, fails, or receives a signal:
```bash
cleanup() {
    rm -f /tmp/lockfile
    echo "Cleanup done"
}
trap cleanup EXIT    # Runs on normal exit AND errors
trap cleanup ERR     # Runs only on errors
trap cleanup SIGINT  # Runs on Ctrl+C
```

I always use `trap` in production scripts to clean up temporary files, release locks, or send notifications on failure. It's like a `finally` block in Java.

---

## 13. What is the difference between `source`, `bash`, and `./` for running scripts?

**Answer:**
- **`./script.sh`** — runs the script in a **new child shell** (subprocess). Variables set in the script are NOT available in the current shell after it finishes.
- **`bash script.sh`** — same as above, runs in a child shell. Doesn't require execute permission.
- **`source script.sh`** or **`. script.sh`** — runs the script in the **current shell**. Variables, functions, and changes (like `cd`) persist after the script finishes.

That's why we run `source ~/.bashrc` after editing it — because we want the changes to take effect in our current session, not in a throwaway subprocess.

---

## 14. How do you use `sed` for text processing?

**Answer:**
`sed` (Stream Editor) processes text line by line. Most common use is **find and replace**:

- `sed 's/old/new/' file.txt` — replace first occurrence on each line
- `sed 's/old/new/g' file.txt` — replace ALL occurrences (global)
- `sed -i 's/old/new/g' file.txt` — edit the file **in-place** (modifies the original)
- `sed '3d' file.txt` — delete line 3
- `sed '/pattern/d' file.txt` — delete lines matching a pattern
- `sed -n '5,10p' file.txt` — print only lines 5-10

In scripts, I use `sed` to update config files, clean log data, or transform text during deployments. For example: `sed -i "s/DB_HOST=.*/DB_HOST=$NEW_HOST/" config.env`.

---

## 15. How do you use `awk` for text processing?

**Answer:**
`awk` is more powerful than `sed` — it's a full programming language for processing **columnar/structured text**:

- `awk '{print $1}' file.txt` — print the first column (space-separated)
- `awk -F: '{print $1, $3}' /etc/passwd` — print username and UID (colon-delimited)
- `awk '$3 > 1000' /etc/passwd` — filter rows where 3rd field > 1000
- `awk '{sum += $5} END {print sum}' report.txt` — sum the 5th column
- `awk 'NR==5' file.txt` — print only line 5

`$0` is the entire line, `$1, $2, ...` are fields, `NR` is the line number, `NF` is the number of fields.

I use `awk` for parsing log files, extracting specific columns from command output, and generating quick reports. For example: `ps aux | awk '$3 > 50 {print $11, $3"%"}'` — find processes using more than 50% CPU.

---

## 16. Write a script to check if a service is running and restart it if not.

**Answer:**
```bash
#!/bin/bash
set -euo pipefail

SERVICE="nginx"
LOG="/var/log/service-monitor.log"

if systemctl is-active --quiet "$SERVICE"; then
    echo "$(date): $SERVICE is running" >> "$LOG"
else
    echo "$(date): $SERVICE is DOWN. Restarting..." >> "$LOG"
    sudo systemctl restart "$SERVICE"
    
    if systemctl is-active --quiet "$SERVICE"; then
        echo "$(date): $SERVICE restarted successfully" >> "$LOG"
    else
        echo "$(date): FAILED to restart $SERVICE" >> "$LOG"
        exit 1
    fi
fi
```

This is a typical health-check script. I'd schedule it with a cron job: `*/5 * * * * /home/kunal/check-service.sh` to run every 5 minutes.

---

## 17. What is a here document (heredoc)?

**Answer:**
A heredoc lets me pass multi-line text to a command. It's cleaner than using multiple `echo` statements:

```bash
cat << EOF
Hello $name,
Today is $(date).
This is a multi-line message.
EOF
```

Variables and commands are expanded inside a heredoc. To prevent expansion, quote the delimiter: `cat << 'EOF'`.

I use heredocs when generating config files, SQL scripts, or multi-line messages in scripts.

---

## 18. How do you debug a shell script?

**Answer:**
Several techniques:

1. **`bash -x script.sh`** — runs in debug mode, printing each command before execution with a `+` prefix. This is the most common debugging technique.

2. **`set -x` / `set +x`** — enable/disable debug mode within specific sections of the script.

3. **`echo` statements** — add print statements at key points to check variable values and flow.

4. **`set -v`** — verbose mode, prints each line as it's read (before expansion).

5. **ShellCheck** — a static analysis tool (`shellcheck script.sh`) that catches common bugs, quoting issues, and bad practices. I install it in CI/CD pipelines.

My debugging workflow: enable `set -x`, run the script, find where it goes wrong, check variable values, fix the issue, and remove the debug flag.

---

## 19. What's the difference between `[` and `[[` in Bash?

**Answer:**
- **`[ ]`** (single bracket) — the POSIX-compliant test command. It's actually an external command (`/usr/bin/[`). Variables must be quoted: `[ "$var" = "hello" ]`. Doesn't support `&&`, `||`, or regex.

- **`[[ ]]`** (double bracket) — a Bash built-in. More powerful and safer: natural `&&` and `||`, regex with `=~`, glob matching with `==`, and handles unquoted variables better (won't break on empty strings).

I always use **`[[ ]]`** when writing Bash scripts. The only reason to use `[ ]` is if the script needs to be POSIX-compatible (run on `sh`, not just `bash`).

---

## 20. How would you write a script that takes a directory as input and archives all `.log` files older than 7 days?

**Answer:**
```bash
#!/bin/bash
set -euo pipefail

DIR="${1:?Usage: $0 <directory>}"
ARCHIVE="/tmp/old_logs_$(date +%Y%m%d).tar.gz"

if [ ! -d "$DIR" ]; then
    echo "Error: $DIR is not a directory"
    exit 1
fi

# Find .log files older than 7 days
OLD_LOGS=$(find "$DIR" -name "*.log" -type f -mtime +7)

if [ -z "$OLD_LOGS" ]; then
    echo "No log files older than 7 days found."
    exit 0
fi

# Archive them
echo "$OLD_LOGS" | tar czf "$ARCHIVE" -T -
echo "Archived to $ARCHIVE"

# Optionally delete the originals
echo "$OLD_LOGS" | xargs rm -f
echo "Old logs deleted."
```

This demonstrates argument validation, `find`, `tar`, and `xargs` — all commonly used in real-world scripts. The `${1:?message}` syntax ensures the argument is provided, and prints the error message if it isn't.
