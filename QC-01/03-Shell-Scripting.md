# Shell Scripting — From Basics to Expert

---

## 1. Introduction to Shell Scripting

### What is a Shell?

A **shell** is a command-line interpreter that provides an interface between the user and the operating system kernel. It reads commands, interprets them, and executes them.

### Types of Shells

| Shell | Path | Description |
|-------|------|-------------|
| **Bash** | `/bin/bash` | Bourne Again Shell — most popular, default on most Linux distros |
| **sh** | `/bin/sh` | Bourne Shell — original Unix shell |
| **zsh** | `/bin/zsh` | Z Shell — extended Bash with more features |
| **ksh** | `/bin/ksh` | Korn Shell — used in enterprise Unix systems |
| **csh** | `/bin/csh` | C Shell — C-like syntax |

```bash
# Check your current shell
echo $SHELL
# Output: /bin/bash

# List all available shells
cat /etc/shells

# Switch shell temporarily
zsh

# Change default shell permanently
chsh -s /bin/zsh
```

### What is a Shell Script?

A shell script is a **text file** containing a sequence of commands that the shell executes. It automates repetitive tasks.

### Under the Hood: How Shell Scripts Execute

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  script.sh   │ ──→ │    Shell     │ ──→ │    Kernel    │
│  (text file) │     │ (interprets  │     │ (executes    │
│              │     │  line by     │     │  system      │
│              │     │  line)       │     │  calls)      │
└──────────────┘     └──────────────┘     └──────────────┘

1. Shell reads the first line (shebang) to know which interpreter to use
2. Shell reads each line, parses it, expands variables, and executes
3. Each command translates to system calls to the kernel
4. Kernel interacts with hardware and returns results
```

### Your First Script

```bash
#!/bin/bash
# This is a comment — the first line is the "shebang"
# It tells the OS which interpreter to use

echo "Hello, World!"
echo "Today is: $(date)"
echo "You are logged in as: $(whoami)"
echo "Your home directory is: $HOME"
```

### Making It Executable and Running

```bash
# Method 1: Make executable and run
chmod +x script.sh
./script.sh

# Method 2: Pass to interpreter directly
bash script.sh

# Method 3: Source it (runs in current shell)
source script.sh
. script.sh
```

> **Under the Hood**: `./script.sh` creates a **new child process** (subshell). `source script.sh` runs in the **current shell** — meaning any variables set in the script persist in your terminal.

---

## 2. Basic Shell Scripting Concepts

Before writing real scripts, you need to understand the building blocks. The most fundamental concept is **variables** — named containers that hold data. Just like in algebra where `x = 5`, in shell scripting you store values in variables and use them later. This makes your scripts flexible — instead of hardcoding values, you use variables so the same script can work with different data.

### Variables

A variable is like a labeled box. You put something in the box (assign a value), and later you can look inside the box (use the variable) to get that value back. In bash, **there must be NO spaces around the `=` sign** when assigning — this is one of the most common mistakes beginners make.

```bash
#!/bin/bash

# Variable assignment — NO SPACES around =
name="Kunal"
age=25
is_admin=true

# Using variables — prefix with $
echo "Name: $name"
echo "Age: $age"

# Curly braces for clarity
echo "Hello, ${name}!"
echo "${name}'s age is ${age}"

# Read-only variables
readonly PI=3.14159
PI=3.0    # Error: PI is a readonly variable

# Unsetting variables
unset age
echo $age   # Nothing — variable is gone
```

### Variable Scoping

Scoping determines **where a variable can be seen and used**. A **global variable** is available everywhere in your script — any function can read and change it. A **local variable** (declared with the `local` keyword inside a function) only exists inside that function — once the function finishes, the variable disappears. Using local variables prevents accidental changes and keeps your code clean and predictable.

```bash
#!/bin/bash

# Global variable (available everywhere in the script)
global_var="I am global"

my_function() {
    # Local variable (only inside this function)
    local local_var="I am local"
    echo $global_var    # Works
    echo $local_var     # Works
}

my_function
echo $global_var        # Works
echo $local_var         # Empty — not accessible outside function
```

### Special Variables

Bash provides built-in special variables that give you useful information automatically. `$0` tells you the script's name, `$1`, `$2`, etc. give you the arguments passed to the script, `$#` tells you how many arguments were passed, and `$?` gives you the exit status of the last command (0 means success, anything else means failure). These are essential for making scripts that accept user input and respond to what happened.

```bash
#!/bin/bash

echo "Script name: $0"           # Name of the script
echo "First argument: $1"        # First positional argument
echo "Second argument: $2"       # Second positional argument
echo "All arguments: $@"         # All arguments as separate strings
echo "All arguments: $*"         # All arguments as single string
echo "Number of arguments: $#"   # Count of arguments
echo "Process ID: $$"            # PID of current script
echo "Last background PID: $!"   # PID of last background process
echo "Exit status: $?"           # Exit code of last command (0=success)
```

### Data Types and Strings

Unlike programming languages like Java or Python, bash has **no real data types**. Everything is treated as a string (text) internally. When you write `num=10`, bash actually stores the text "10", not the number 10. For math operations, you need special syntax like `$(( ))` or the `bc` calculator. This is important to understand because it affects how you compare values and do calculations.

Strings in bash can be manipulated in many useful ways — you can get their length, extract parts of them (substrings), replace text within them, or convert to uppercase/lowercase. Understanding the difference between **single quotes** (`' '`) and **double quotes** (`" "`) is critical: double quotes expand variables (so `"Hello $name"` becomes `"Hello Kunal"`), while single quotes keep everything literal (so `'Hello $name'` stays exactly as `'Hello $name'`).

```bash
#!/bin/bash

# Bash variables are UNTYPED (everything is a string internally)

# String operations
str="Hello World"
echo ${#str}           # Length: 11
echo ${str:0:5}        # Substring: Hello (start:length)
echo ${str:6}          # From position 6: World
echo ${str/World/Bash} # Replace: Hello Bash
echo ${str^^}          # Uppercase: HELLO WORLD
echo ${str,,}          # Lowercase: hello world

# String concatenation
first="Hello"
second="World"
result="${first} ${second}"
echo $result           # Hello World

# Single vs Double quotes
name="Kunal"
echo "Hello $name"     # Hello Kunal (variables expanded)
echo 'Hello $name'     # Hello $name (literal — no expansion)
echo "Today is $(date)" # Command substitution works in double quotes

# Arithmetic
declare -i num=10       # Declare as integer
num=num+5               # Works because declared as integer
echo $num               # 15

# Or use arithmetic expansion
result=$((5 + 3))
echo $result            # 8

result=$(( 10 * 2 + 5 ))
echo $result            # 25

# Floating point (bash doesn't support — use bc)
result=$(echo "scale=2; 10 / 3" | bc)
echo $result            # 3.33
```

### Arrays

An array is a variable that can hold **multiple values** instead of just one. Think of it like a numbered list. Instead of creating separate variables like `fruit1="apple"`, `fruit2="banana"`, `fruit3="cherry"`, you create one array `fruits=("apple" "banana" "cherry")` and access each item by its position number (index). Bash arrays start counting from 0, so the first item is `${fruits[0]}`.

Bash 4+ also supports **associative arrays** (like dictionaries or maps) where you can use text keys instead of numbers — for example, `person[name]="Kunal"`. These are very useful when you need to store key-value pairs.

```bash
#!/bin/bash

# Indexed array
fruits=("apple" "banana" "cherry" "date" "elderberry")

echo ${fruits[0]}         # First element: apple
echo ${fruits[2]}         # Third element: cherry
echo ${fruits[@]}         # All elements
echo ${#fruits[@]}        # Length: 5
echo ${fruits[@]:1:3}     # Slice: banana cherry date

# Add element
fruits+=("fig")

# Delete element
unset fruits[1]           # Removes "banana" (index remains, value gone)

# Loop through array
for fruit in "${fruits[@]}"; do
    echo "Fruit: $fruit"
done

# Associative array (dictionary/map) — Bash 4+
declare -A person
person[name]="Kunal"
person[age]=25
person[city]="Mumbai"

echo ${person[name]}      # Kunal
echo ${!person[@]}        # All keys: name age city
echo ${person[@]}         # All values: Kunal 25 Mumbai

for key in "${!person[@]}"; do
    echo "$key: ${person[$key]}"
done
```

---

## 3. Control Structures

Control structures let your script **make decisions** and **repeat actions**. Without them, your script would just run every line from top to bottom, always doing the same thing. With control structures, your script can say "if this condition is true, do this; otherwise, do that" or "repeat this action 100 times" or "keep doing this until the user says stop."

These are the brain of your script — they give it logic and intelligence.

### If-Else

The `if` statement checks a condition and runs different code depending on whether it's true or false. This is the most basic decision-making tool. You can chain multiple conditions with `elif` (else-if) to handle several possibilities. **Important**: in bash, the spaces inside `[ ]` are required — `[ $age -ge 18 ]` works but `[$age -ge 18]` does NOT.

```bash
#!/bin/bash

# Basic if
age=20

if [ $age -ge 18 ]; then
    echo "Adult"
fi

# If-else
if [ $age -ge 18 ]; then
    echo "Adult"
else
    echo "Minor"
fi

# If-elif-else
score=75

if [ $score -ge 90 ]; then
    echo "Grade: A"
elif [ $score -ge 80 ]; then
    echo "Grade: B"
elif [ $score -ge 70 ]; then
    echo "Grade: C"
elif [ $score -ge 60 ]; then
    echo "Grade: D"
else
    echo "Grade: F"
fi
```

### Comparison Operators

Bash uses different operators for comparing numbers vs. comparing strings. For numbers, you use flags like `-eq` (equal), `-gt` (greater than), `-lt` (less than). For strings, you use `=` (equal) and `!=` (not equal). There are also file test operators that check things about files — does this file exist? Is it readable? Is it a directory? These are used constantly in real scripts to verify files before working with them.

```bash
# Numeric comparisons (use inside [ ])
-eq    # Equal
-ne    # Not equal
-gt    # Greater than
-ge    # Greater than or equal
-lt    # Less than
-le    # Less than or equal

# String comparisons
=      # Equal (or ==)
!=     # Not equal
-z     # String is empty (zero length)
-n     # String is not empty
<      # Less than (alphabetically)
>      # Greater than (alphabetically)

# File tests
-f     # Is a regular file
-d     # Is a directory
-e     # Exists (file or directory)
-r     # Is readable
-w     # Is writable
-x     # Is executable
-s     # File is not empty (size > 0)
-L     # Is a symbolic link

# Logical operators
-a     # AND (inside [ ])
-o     # OR (inside [ ])
!      # NOT

# Examples:
if [ -f "/etc/passwd" ]; then
    echo "File exists"
fi

if [ -z "$name" ]; then
    echo "Name is empty"
fi

if [ "$str1" = "$str2" ]; then
    echo "Strings are equal"
fi
```

### Modern Test: `[[ ]]` (Bash-specific)

`[[ ]]` is an improved version of `[ ]` that's specific to bash. It's safer (you don't need to quote variables), supports pattern matching (like `K*` to match anything starting with K), and lets you use regex for complex pattern matching. It also uses `&&` and `||` for logical AND/OR instead of the older `-a` and `-o`. If you're writing bash scripts (not POSIX-portable sh scripts), always prefer `[[ ]]`.

```bash
#!/bin/bash

# [[ ]] is the modern version — safer and more features
name="Kunal"

# Pattern matching
if [[ $name == K* ]]; then
    echo "Name starts with K"
fi

# Regex matching
email="kunal@example.com"
if [[ $email =~ ^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$ ]]; then
    echo "Valid email"
fi

# No need to quote variables (safer)
if [[ -z $name ]]; then
    echo "Empty"
fi

# Logical operators use && and ||
if [[ $age -ge 18 && $age -le 65 ]]; then
    echo "Working age"
fi
```

### Case (Switch) Statement

The `case` statement is a cleaner way to check a variable against multiple specific values. Instead of writing a long chain of `if-elif-elif-elif`, you use `case` to match patterns. Each pattern ends with `)`, the code for that match ends with `;;`, and the whole block ends with `esac` (which is "case" spelled backwards). The `*)` pattern acts as a default catch-all for anything that doesn't match the other patterns.

```bash
#!/bin/bash

read -p "Enter a fruit: " fruit

case $fruit in
    "apple")
        echo "Apples are red"
        ;;
    "banana")
        echo "Bananas are yellow"
        ;;
    "cherry" | "strawberry")     # Multiple patterns
        echo "These are small fruits"
        ;;
    *)                            # Default case
        echo "Unknown fruit: $fruit"
        ;;
esac
```

### Loops

Loops let you repeat a block of code multiple times. There are three types:
- **`for` loop**: Use when you know in advance what you want to iterate over (a list of items, a range of numbers, or files in a directory)
- **`while` loop**: Use when you want to keep going as long as a condition is true (like reading a file line by line)
- **`until` loop**: The opposite of `while` — it keeps going until the condition becomes true

Loops are essential for automation — if you need to process 100 files or run a health check every 5 seconds, loops are how you do it.

```bash
#!/bin/bash

# For loop — iterate over list
for color in red green blue yellow; do
    echo "Color: $color"
done

# For loop — C-style
for ((i = 0; i < 5; i++)); do
    echo "Number: $i"
done

# For loop — range
for i in {1..10}; do
    echo "Count: $i"
done

# For loop — range with step
for i in {0..100..10}; do
    echo "Tens: $i"       # 0, 10, 20, ... 100
done

# For loop — iterate over files
for file in *.txt; do
    echo "Processing: $file"
done

# While loop
count=1
while [ $count -le 5 ]; do
    echo "Count: $count"
    ((count++))
done

# While loop — read file line by line
while IFS= read -r line; do
    echo "Line: $line"
done < input.txt

# Until loop (opposite of while — runs UNTIL condition is true)
count=1
until [ $count -gt 5 ]; do
    echo "Count: $count"
    ((count++))
done

# Loop control
for i in {1..10}; do
    if [ $i -eq 3 ]; then
        continue    # Skip iteration 3
    fi
    if [ $i -eq 8 ]; then
        break       # Exit loop at 8
    fi
    echo $i         # Prints: 1 2 4 5 6 7
done

# Infinite loop
while true; do
    echo "Press Ctrl+C to stop"
    sleep 1
done
```

### Select (Menu)

```bash
#!/bin/bash

echo "Select your OS:"
select os in "Linux" "macOS" "Windows" "Quit"; do
    case $os in
        "Linux") echo "You chose Linux"; break ;;
        "macOS") echo "You chose macOS"; break ;;
        "Windows") echo "You chose Windows"; break ;;
        "Quit") echo "Goodbye!"; exit 0 ;;
        *) echo "Invalid option" ;;
    esac
done
```

---

## 4. Command-Line Arguments

Command-line arguments let you pass information to your script when you run it, instead of hardcoding values inside the script. For example, instead of writing a script that always backs up `/home/kunal`, you can write one that accepts any directory as an argument: `./backup.sh /home/kunal`. This makes your scripts reusable for different situations.

Arguments are accessed using `$1` (first argument), `$2` (second argument), and so on. `$0` is always the script's own name, and `$#` tells you how many arguments were provided.

### Accessing Arguments

```bash
#!/bin/bash
# Save as: greet.sh
# Run as: ./greet.sh Kunal 25

echo "Script: $0"         # ./greet.sh
echo "Name: $1"           # Kunal
echo "Age: $2"            # 25
echo "All args: $@"       # Kunal 25
echo "Arg count: $#"      # 2
```

### Parsing Options with `getopts`

`getopts` is a built-in tool for handling **flags and options** like `-v` for verbose mode or `-p 8080` for a port number. Instead of having users remember the order of arguments ("was it directory first or port first?"), flags let them pass arguments in any order: `./deploy.sh -p 8080 -e production -v`. The colon after a letter (like `e:`) means that option requires a value; no colon (like `v`) means it's a boolean flag.

```bash
#!/bin/bash
# Save as: deploy.sh
# Run as: ./deploy.sh -e production -v -p 8080

environment="development"
verbose=false
port=3000

while getopts "e:vp:h" opt; do
    case $opt in
        e) environment=$OPTARG ;;
        v) verbose=true ;;
        p) port=$OPTARG ;;
        h)
            echo "Usage: $0 [-e environment] [-v] [-p port]"
            echo "  -e  Environment (default: development)"
            echo "  -v  Verbose mode"
            echo "  -p  Port number (default: 3000)"
            exit 0
            ;;
        \?)
            echo "Invalid option: -$OPTARG" >&2
            exit 1
            ;;
    esac
done

echo "Environment: $environment"
echo "Verbose: $verbose"
echo "Port: $port"
```

### Argument Validation

Good scripts always **validate their inputs** before doing anything. Check that the user provided enough arguments, verify that input files actually exist, and make sure directories are valid. If something is wrong, print a helpful error message and exit with a non-zero exit code. This prevents your script from doing something unexpected or dangerous (like deleting the wrong files) when given bad input.

```bash
#!/bin/bash

# Check minimum arguments
if [ $# -lt 2 ]; then
    echo "Usage: $0 <source> <destination>"
    exit 1
fi

source_file=$1
dest_file=$2

# Validate file exists
if [ ! -f "$source_file" ]; then
    echo "Error: Source file '$source_file' not found"
    exit 1
fi

# Validate directory exists
dest_dir=$(dirname "$dest_file")
if [ ! -d "$dest_dir" ]; then
    echo "Error: Destination directory '$dest_dir' does not exist"
    exit 1
fi

cp "$source_file" "$dest_file"
echo "Copied $source_file to $dest_file"
```

### Shift — Processing Arguments

```bash
#!/bin/bash

# shift moves arguments left: $2→$1, $3→$2, etc.
while [ $# -gt 0 ]; do
    echo "Processing: $1"
    shift
done

# Example: ./script.sh a b c d
# Output:
# Processing: a
# Processing: b
# Processing: c
# Processing: d
```

---

## 5. Functions

Functions are **reusable blocks of code** that you give a name to. Instead of writing the same 10 lines of code multiple times in your script, you put them inside a function and then call that function whenever you need it. Functions make your scripts shorter, easier to read, and easier to maintain. If you need to change the logic, you change it in one place instead of everywhere it's used.

Functions in bash are simpler than in other programming languages — they don't have formal parameter declarations, and they "return" values by printing to stdout (using `echo`) rather than using a `return` keyword (which is only for exit codes 0-255).

### Defining and Calling Functions

```bash
#!/bin/bash

# Method 1: function keyword
function greet() {
    echo "Hello, $1!"
}

# Method 2: without function keyword (POSIX compatible)
add() {
    local result=$(( $1 + $2 ))
    echo $result
}

# Calling functions
greet "Kunal"           # Hello, Kunal!
sum=$(add 5 3)          # Capture output
echo "Sum: $sum"        # Sum: 8
```

### Return Values

In bash, `return` only sends back an **exit code** (a number 0-255). It's NOT like `return` in Java or Python which can send back any value. To send back actual data (strings, numbers, etc.), the function should `echo` the result, and the caller captures it with `$(function_name)`. This is a common source of confusion for beginners — remember: `return` = exit code, `echo` = actual value.

```bash
#!/bin/bash

# Functions can return exit codes (0-255) via 'return'
# For actual values, use echo + command substitution

is_even() {
    if (( $1 % 2 == 0 )); then
        return 0    # 0 = true/success in bash
    else
        return 1    # non-zero = false/failure
    fi
}

# Using return code
is_even 4
if [ $? -eq 0 ]; then
    echo "4 is even"
fi

# Shorter version
if is_even 6; then
    echo "6 is even"
fi

# Returning a string value
get_greeting() {
    local name=$1
    local time_of_day=$2
    echo "Good $time_of_day, $name!"    # "returns" via stdout
}

message=$(get_greeting "Kunal" "morning")
echo $message    # Good morning, Kunal!
```

### Advanced Function Patterns

```bash
#!/bin/bash

# Function with default parameters
connect() {
    local host=${1:-"localhost"}
    local port=${2:-3306}
    local user=${3:-"root"}
    echo "Connecting to $user@$host:$port"
}

connect                           # localhost:3306 as root
connect "db.example.com"          # db.example.com:3306 as root
connect "db.example.com" 5432 "admin"  # Full custom

# Function library — source from another file
# file: lib/utils.sh
log_info() {
    echo "[INFO] $(date '+%Y-%m-%d %H:%M:%S') - $1"
}

log_error() {
    echo "[ERROR] $(date '+%Y-%m-%d %H:%M:%S') - $1" >&2
}

log_warn() {
    echo "[WARN] $(date '+%Y-%m-%d %H:%M:%S') - $1"
}

# file: main.sh
# source lib/utils.sh
# log_info "Application started"
# log_error "Database connection failed"
```

---

## 6. Text Processing

Text processing is one of the most powerful features of shell scripting. Two tools dominate this space: **`sed`** (Stream Editor) and **`awk`** (named after its creators: Aho, Weinberger, and Kernighan). Together they can transform, filter, extract, and reformat any text-based data — log files, CSV files, configuration files, code, and more.

**`sed`** is best for simple find-and-replace operations and editing files. Think of it as a programmable "Find & Replace" dialog. **`awk`** is more powerful — it treats each line as a record with fields (like columns in a spreadsheet) and lets you do calculations, filtering, and formatting. For quick, simple text work use `sed`; for anything involving columns or math, use `awk`.

### `sed` — Stream Editor

`sed` reads input line by line, applies your rules, and outputs the result. The most common use is substitution (replacing text): `sed 's/old/new/g'` replaces all occurrences of "old" with "new". The `s` means substitute, the `g` at the end means global (all occurrences on a line, not just the first one). You can also use `sed` to delete specific lines, insert new lines, or print only lines that match a pattern.

```bash
#!/bin/bash

# sed performs text transformations on files or streams

# Substitute (replace) — first occurrence per line
sed 's/old/new/' file.txt

# Substitute — all occurrences per line (global)
sed 's/old/new/g' file.txt

# Case-insensitive replace
sed 's/old/new/gi' file.txt

# Replace in-place (edit file directly)
sed -i 's/old/new/g' file.txt

# Delete lines
sed '5d' file.txt           # Delete line 5
sed '3,7d' file.txt         # Delete lines 3-7
sed '/pattern/d' file.txt   # Delete lines matching pattern
sed '/^$/d' file.txt        # Delete empty lines

# Print specific lines
sed -n '10p' file.txt       # Print only line 10
sed -n '5,10p' file.txt     # Print lines 5-10

# Insert/Append
sed '3i\New line before 3' file.txt   # Insert before line 3
sed '3a\New line after 3' file.txt    # Append after line 3

# Multiple operations
sed -e 's/foo/bar/g' -e 's/baz/qux/g' file.txt

# Practical example: Update config file
sed -i 's/port=8080/port=9090/g' config.properties
```

### `awk` — Pattern Scanning and Processing

`awk` is like a mini programming language designed for text processing. It automatically splits each line into fields (by default, separated by whitespace). `$1` is the first field, `$2` is the second, and so on. `$0` is the entire line. `NR` is the current line number and `NF` is how many fields the current line has.

The basic pattern is: `awk 'condition { action }' file` — for each line, if the condition is true, do the action. If you skip the condition, the action runs on every line. This makes `awk` incredibly flexible for filtering and transforming structured data.

```bash
#!/bin/bash

# awk processes text line by line, splitting each into fields

# Basic syntax: awk 'pattern {action}' file

# Print specific fields (default delimiter: whitespace)
echo "John 25 Engineer" | awk '{print $1}'       # John
echo "John 25 Engineer" | awk '{print $1, $3}'   # John Engineer

# Custom delimiter
echo "John,25,Engineer" | awk -F',' '{print $1, $3}'  # John Engineer

# Print with formatting
awk '{printf "%-15s %5d\n", $1, $2}' data.txt

# Filter by condition
awk '$3 > 50000 {print $1, $3}' employees.txt    # Salary > 50000

# Built-in variables
awk '{print NR": "$0}' file.txt      # NR = line number, $0 = whole line
awk 'END {print NR}' file.txt        # Total number of lines
awk '{print NF}' file.txt            # NF = number of fields per line

# Sum a column
awk '{sum += $2} END {print "Total:", sum}' data.txt

# Average
awk '{sum += $2; count++} END {print "Average:", sum/count}' data.txt

# Practical: Parse CSV and generate report
awk -F',' '
    NR > 1 {
        sum += $3
        count++
        if ($3 > max) max = $3
        if (min == "" || $3 < min) min = $3
    }
    END {
        print "Records:", count
        print "Total:", sum
        print "Average:", sum/count
        print "Max:", max
        print "Min:", min
    }
' sales.csv

# Print lines between two patterns
awk '/START/,/END/' file.txt
```

### Combining Tools

```bash
#!/bin/bash

# Parse Apache access log — top 10 IPs
cat access.log | awk '{print $1}' | sort | uniq -c | sort -rn | head -10

# Find all TODO comments in code
grep -rn "TODO" --include="*.java" src/ | awk -F: '{printf "%s (line %s): %s\n", $1, $2, $3}'

# CSV to formatted table
cat data.csv | awk -F',' '{printf "| %-15s | %-10s | %8s |\n", $1, $2, $3}'

# Extract email addresses from a file
grep -oE '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}' file.txt | sort -u

# Replace multiple spaces with single space
sed 's/  */ /g' file.txt

# Count word frequency in a file
cat file.txt | tr '[:upper:]' '[:lower:]' | tr -s ' ' '\n' | sort | uniq -c | sort -rn | head -20
```

---

## 7. Error Handling and Debugging

When things go wrong in your script (and they will), you need two things: a way to **handle errors gracefully** (instead of crashing or doing the wrong thing) and tools to **find and fix bugs** (debugging).

Error handling means checking if commands succeeded before continuing, cleaning up temporary files even if the script fails, and giving users helpful error messages. Debugging means finding out what your script is actually doing when something goes wrong — which lines ran, what values the variables had, and where exactly the problem is.

### Exit Codes

Every command in Linux returns an **exit code** when it finishes — `0` means success, anything else (1-255) means something went wrong. Your scripts should both **check** exit codes (to detect when something fails) and **set** exit codes (to tell the caller whether your script succeeded). The special variable `$?` always holds the exit code of the most recently executed command.

```bash
#!/bin/bash

# Every command returns an exit code:
# 0 = success
# 1-255 = failure (different codes mean different errors)

ls /nonexistent 2>/dev/null
echo "Exit code: $?"    # 2 (No such file or directory)

# Set exit codes in your scripts
exit 0    # Success
exit 1    # General error

# Custom exit codes
readonly E_SUCCESS=0
readonly E_FILE_NOT_FOUND=2
readonly E_PERMISSION_DENIED=3
readonly E_INVALID_ARGS=4
```

### Error Handling Patterns

There are several strategies for handling errors in bash scripts:
- **Check `$?` manually** after each important command
- **Use `||`** to run a fallback command when something fails (like `command || echo "Failed!"`)
- **Use `set -e`** to make your script exit immediately if ANY command fails (most popular approach)
- **Use `trap`** to run cleanup code when the script exits or encounters an error (like deleting temporary files)

The best practice is to put `set -euo pipefail` at the top of every script. This makes the script exit on errors (`-e`), treat unset variables as errors (`-u`), and catch errors in piped commands (`-o pipefail`).

```bash
#!/bin/bash

# Method 1: Check $? after each command
cp source.txt dest.txt
if [ $? -ne 0 ]; then
    echo "Copy failed!" >&2
    exit 1
fi

# Method 2: Short-circuit with || 
cp source.txt dest.txt || { echo "Copy failed!" >&2; exit 1; }

# Method 3: set -e (exit on any error)
set -e          # Script exits immediately if any command fails
set -u          # Treat unset variables as errors
set -o pipefail # Pipe fails if any command in pipe fails

# Combine all three (best practice)
set -euo pipefail

# Method 4: trap — execute on error/exit
cleanup() {
    echo "Cleaning up temporary files..."
    rm -f /tmp/myapp_*
}

error_handler() {
    echo "Error on line $1" >&2
    cleanup
    exit 1
}

trap 'error_handler $LINENO' ERR
trap cleanup EXIT

# Method 5: Try-catch equivalent
{
    # Try
    risky_command
} || {
    # Catch
    echo "risky_command failed, trying alternative..."
    alternative_command
}
```

### Debugging Techniques

When your script doesn't work as expected, you need to see what's happening behind the scenes. The most useful technique is **`set -x`** (or running `bash -x script.sh`), which prints every command before executing it, showing you exactly what your script is doing and what values the variables have. You can turn it on (`set -x`) and off (`set +x`) around specific sections to focus on the problematic area.

For more professional debugging, you can use **`PS4`** to customize the debug output to show file names and line numbers, write your own logging functions, or use **`shellcheck`** (an external tool) to find bugs and bad practices before you even run your script.

```bash
#!/bin/bash

# Method 1: Run with debug flag
bash -x script.sh          # Print each command before executing

# Method 2: Enable debug in script
set -x      # Turn on debug (shows each command)
# ... code ...
set +x      # Turn off debug

# Method 3: Debug specific sections
set -x
problematic_code_here
set +x

# Method 4: PS4 — customize debug prompt
export PS4='+(${BASH_SOURCE}:${LINENO}): ${FUNCNAME[0]:+${FUNCNAME[0]}(): }'
set -x
# Now debug output shows file, line number, and function name

# Method 5: Logging function
DEBUG=true

debug_log() {
    if [ "$DEBUG" = true ]; then
        echo "[DEBUG] $(date '+%H:%M:%S') - $1" >&2
    fi
}

debug_log "Processing file: $filename"
debug_log "Variable value: count=$count"

# Method 6: Validate with shellcheck (external tool)
# Install: sudo apt install shellcheck
# Run: shellcheck script.sh
```

---

## 8. Real-World Script Examples

The best way to learn shell scripting is by seeing complete, real-world examples. These scripts combine everything we've covered — variables, functions, loops, error handling, and text processing — into practical tools you might actually use at work. Each example follows best practices like `set -euo pipefail` for safety and proper logging for visibility.

### Example 1: Automated Backup Script

```bash
#!/bin/bash
set -euo pipefail

# Configuration
BACKUP_DIR="/backups"
SOURCE_DIR="/home/kunal/projects"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="${BACKUP_DIR}/backup_${DATE}.tar.gz"
MAX_BACKUPS=7

# Logging
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1"
}

# Create backup directory if not exists
mkdir -p "$BACKUP_DIR"

# Create backup
log "Starting backup of $SOURCE_DIR"
tar -czf "$BACKUP_FILE" -C "$(dirname "$SOURCE_DIR")" "$(basename "$SOURCE_DIR")"
log "Backup created: $BACKUP_FILE ($(du -h "$BACKUP_FILE" | cut -f1))"

# Remove old backups (keep only last N)
backup_count=$(ls -1 "$BACKUP_DIR"/backup_*.tar.gz 2>/dev/null | wc -l)
if [ "$backup_count" -gt "$MAX_BACKUPS" ]; then
    remove_count=$((backup_count - MAX_BACKUPS))
    ls -1t "$BACKUP_DIR"/backup_*.tar.gz | tail -n "$remove_count" | xargs rm -f
    log "Removed $remove_count old backup(s)"
fi

log "Backup complete. Total backups: $(ls -1 "$BACKUP_DIR"/backup_*.tar.gz | wc -l)"
```

### Example 2: System Health Check

```bash
#!/bin/bash

echo "========================================="
echo "  System Health Check — $(date)"
echo "========================================="

# CPU Usage
echo ""
echo "--- CPU Usage ---"
top -bn1 | head -5

# Memory Usage
echo ""
echo "--- Memory Usage ---"
free -h

# Disk Usage
echo ""
echo "--- Disk Usage ---"
df -h | grep -E '^/dev'

# Check if disk usage > 80%
echo ""
echo "--- Disk Alerts ---"
df -h | awk 'NR>1 {
    gsub(/%/,"",$5)
    if ($5+0 > 80)
        printf "⚠️  WARNING: %s is %s%% full (%s)\n", $6, $5, $1
}'

# Top 5 processes by memory
echo ""
echo "--- Top 5 Processes (Memory) ---"
ps aux --sort=-%mem | head -6

# Network connectivity
echo ""
echo "--- Network ---"
ping -c 1 -W 2 google.com > /dev/null 2>&1 && echo "Internet: Connected" || echo "Internet: Disconnected"

echo ""
echo "========================================="
echo "  Check complete"
echo "========================================="
```

### Example 3: Log Analyzer

```bash
#!/bin/bash
set -euo pipefail

LOG_FILE=${1:-"/var/log/syslog"}

if [ ! -f "$LOG_FILE" ]; then
    echo "Error: Log file not found: $LOG_FILE" >&2
    exit 1
fi

echo "Analyzing: $LOG_FILE"
echo "========================"

echo ""
echo "Total lines: $(wc -l < "$LOG_FILE")"

echo ""
echo "--- Error Summary ---"
grep -ci "error" "$LOG_FILE" 2>/dev/null && true
echo "errors found"

echo ""
echo "--- Warning Summary ---"
grep -ci "warning" "$LOG_FILE" 2>/dev/null && true
echo "warnings found"

echo ""
echo "--- Last 10 Errors ---"
grep -i "error" "$LOG_FILE" | tail -10

echo ""
echo "--- Errors by Hour ---"
grep -i "error" "$LOG_FILE" | awk '{print $3}' | cut -d: -f1 | sort | uniq -c | sort -rn
```
