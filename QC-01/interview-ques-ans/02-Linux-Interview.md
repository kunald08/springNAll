# Linux — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. Answers are written exactly how you should speak in an interview — clear, confident, plain English.

---

## 1. What is Linux? Why is it used in enterprise environments?

**Answer:**
Linux is an **open-source, Unix-like operating system** built around the Linux kernel, originally created by Linus Torvalds in 1991. It's the backbone of most enterprise environments because it's **free, highly stable, secure, and extremely customizable**.

Almost all servers, cloud infrastructure (AWS, Azure, GCP), and containers (Docker/Kubernetes) run on Linux. As a developer, I need to be comfortable with Linux because that's where our applications are deployed and run in production.

Common distributions include **Ubuntu, CentOS, Red Hat Enterprise Linux (RHEL), Amazon Linux**, and **Debian**.

---

## 2. Explain the Linux filesystem hierarchy.

**Answer:**
Linux has a **single root directory** `/` and everything branches from it — there are no drive letters like Windows.

The key directories are:
- `/` — root of the entire filesystem
- `/home` — user home directories (like `C:\Users` in Windows)
- `/root` — home directory for the root (admin) user
- `/etc` — system configuration files (like `passwd`, `hosts`, `nginx.conf`)
- `/var` — variable data like logs (`/var/log`), databases, mail
- `/tmp` — temporary files (cleared on reboot)
- `/bin` — essential user commands (`ls`, `cp`, `cat`)
- `/sbin` — system administration commands (`shutdown`, `mount`)
- `/usr` — user programs and libraries
- `/opt` — optional/third-party software
- `/dev` — device files (hardware represented as files)
- `/proc` — virtual filesystem for process and system info

The philosophy is **"everything is a file"** — even hardware devices and processes are represented as files.

---

## 3. What's the difference between absolute and relative paths?

**Answer:**
An **absolute path** starts from the root `/` and gives the complete location — like `/home/kunal/projects/app.java`. It works from anywhere in the system.

A **relative path** starts from the current directory. If I'm in `/home/kunal`, then `projects/app.java` points to the same file. `.` means current directory, `..` means parent directory.

For scripts and automation, I prefer absolute paths because they're unambiguous. For daily navigation, relative paths are faster.

---

## 4. What are the basic Linux navigation commands?

**Answer:**
- `pwd` — "print working directory" — shows where I am right now
- `ls` — lists files in the current directory. I commonly use `ls -la` which shows all files (including hidden ones starting with `.`) in long format with permissions, owner, size, and date.
- `cd` — change directory. `cd /var/log` goes to an absolute path. `cd ..` goes up one level. `cd ~` or just `cd` goes to home directory. `cd -` goes to the previous directory.
- `clear` — clears the terminal screen
- `whoami` — shows the current logged-in user
- `hostname` — shows the machine's hostname
- `uname -a` — shows system information (kernel version, architecture)

---

## 5. How do you create, copy, move, and delete files and directories?

**Answer:**
For **files**:
- `touch file.txt` — creates an empty file (or updates its timestamp)
- `cp file.txt backup.txt` — copies a file
- `mv file.txt newname.txt` — renames or moves a file
- `rm file.txt` — deletes a file (no recycle bin!)
- `rm -f file.txt` — force delete without confirmation

For **directories**:
- `mkdir mydir` — create a directory
- `mkdir -p parent/child/grandchild` — create nested directories
- `cp -r dir1 dir2` — copy directory recursively
- `mv dir1 dir2` — move or rename a directory
- `rm -r mydir` — delete directory and all contents
- `rm -rf mydir` — force delete recursively (use with extreme caution!)

The `-r` flag means **recursive** — required for directories because they can contain other files and directories inside them.

---

## 6. Explain Linux file permissions.

**Answer:**
Every file has three sets of permissions for three categories of users:

- **Owner (u)** — the user who created the file
- **Group (g)** — a group of users
- **Others (o)** — everyone else

Each set has three permissions:
- **r (read = 4)** — can view file contents or list directory
- **w (write = 2)** — can modify file or add/remove files in directory
- **x (execute = 1)** — can run the file as a program or enter the directory

When I do `ls -l`, I see something like `-rwxr-xr--`. Breaking it down:
- First character: `-` = file, `d` = directory
- `rwx` — owner has read + write + execute
- `r-x` — group has read + execute
- `r--` — others have read only

The **numeric representation** adds up the values: `rwxr-xr--` = 754 (owner: 7, group: 5, others: 4).

---

## 7. How do you change permissions and ownership?

**Answer:**
**Permissions** — using `chmod`:
- Numeric: `chmod 755 script.sh` — owner gets full, group and others get read + execute
- Symbolic: `chmod u+x script.sh` — add execute for owner
- `chmod g-w file.txt` — remove write from group
- `chmod a+r file.txt` — add read for all (a = all)
- `chmod -R 755 mydir` — recursively change permissions for a directory

**Ownership** — using `chown`:
- `chown kunal file.txt` — change owner
- `chown kunal:devteam file.txt` — change owner and group
- `chown -R kunal:devteam mydir` — recursively change ownership

Only the root user or the file owner can change permissions. Only root can change ownership.

---

## 8. What is the difference between `cat`, `less`, `more`, `head`, and `tail`?

**Answer:**
All are used to view file contents, but differently:

- `cat file.txt` — dumps the entire file to the screen. Good for small files. Can also concatenate files: `cat file1 file2 > combined.txt`.
- `less file.txt` — opens an interactive viewer where I can scroll up/down, search with `/keyword`, and quit with `q`. Best for large files.
- `more file.txt` — older version of `less`, can only scroll forward.
- `head file.txt` — shows the first 10 lines. `head -n 20` shows first 20.
- `tail file.txt` — shows the last 10 lines. `tail -n 20` shows last 20.
- `tail -f logfile.log` — **follows** the file in real-time, great for monitoring live logs.

In practice, I use `cat` for small files, `less` for reading large files, and `tail -f` for monitoring logs.

---

## 9. How do you search for text inside files?

**Answer:**
The primary tool is **`grep`** (Global Regular Expression Print):

- `grep "error" logfile.log` — find lines containing "error"
- `grep -i "error" logfile.log` — case-insensitive search
- `grep -r "TODO" .` — recursively search in all files from current directory
- `grep -n "error" logfile.log` — show line numbers
- `grep -c "error" logfile.log` — count matching lines
- `grep -v "debug" logfile.log` — show lines that do NOT match (invert)
- `grep -l "error" *.log` — show only filenames that contain the match

I can also use regular expressions: `grep -E "error|warning|fatal" logfile.log` searches for any of those words.

For finding files by name, I use `find`:
- `find /var/log -name "*.log"` — find all `.log` files under `/var/log`
- `find . -type f -name "*.java"` — find Java files in current directory
- `find . -type f -mtime -7` — files modified in the last 7 days

---

## 10. What are pipes and redirection in Linux?

**Answer:**
**Pipes (`|`)** take the output of one command and feed it as input to the next command. They let me chain commands together:

- `cat logfile.log | grep "error" | wc -l` — count how many error lines are in a log file
- `ps aux | grep java` — find running Java processes
- `ls -la | sort -k5 -n` — list files sorted by size

**Redirection** sends output to a file instead of the screen:
- `>` — redirect output (overwrites): `echo "hello" > file.txt`
- `>>` — redirect output (appends): `echo "world" >> file.txt`
- `<` — redirect input: `sort < unsorted.txt`
- `2>` — redirect errors: `command 2> errors.log`
- `&>` — redirect both output and errors: `command &> all.log`
- `/dev/null` — the "black hole" — `command 2>/dev/null` discards errors

These are fundamental to writing shell scripts and one-liners for automation.

---

## 11. How do you manage processes in Linux?

**Answer:**
Key commands:
- `ps aux` — list all running processes with details (PID, CPU, memory, command)
- `top` or `htop` — real-time system monitor (CPU, memory, processes)
- `kill PID` — send SIGTERM to a process (graceful shutdown)
- `kill -9 PID` — send SIGKILL (force kill — use as last resort)
- `killall java` — kill all processes by name

Background/foreground:
- `command &` — run a command in the background
- `jobs` — list background jobs
- `fg %1` — bring job 1 to foreground
- `Ctrl+Z` — suspend the current process
- `bg` — resume suspended process in background
- `nohup command &` — run command that survives after I log out

To find a process: `ps aux | grep "app-name"` or `pgrep -f "app-name"`.

---

## 12. What is the difference between `su` and `sudo`?

**Answer:**
- `su` — "switch user." `su root` switches to the root user entirely. You need the root password. You become root until you type `exit`.

- `sudo` — "superuser do." Runs a SINGLE command as root. I use my OWN password, not root's. After the command runs, I'm back to my normal user.

`sudo` is preferred because:
1. I don't need to know the root password.
2. There's an audit trail — `/var/log/auth.log` records who ran what.
3. I can grant specific sudo permissions per user in `/etc/sudoers`.

The sudoers file (`/etc/sudoers`) is edited with `visudo` to prevent syntax errors that could lock everyone out.

---

## 13. How do you check disk space and memory usage?

**Answer:**
**Disk space:**
- `df -h` — shows disk usage for all mounted filesystems in human-readable format (GB, MB)
- `du -sh /var/log` — shows the total size of a specific directory
- `du -sh *` — shows size of each item in current directory

**Memory:**
- `free -h` — shows total, used, free, and available memory (RAM and swap)
- `top` or `htop` — real-time view of memory and CPU per process

**CPU:**
- `top` — real-time CPU usage
- `nproc` — number of CPU cores
- `uptime` — system load averages

In production, if disk is full, I'd check `/var/log` first — logs are the most common space hog. I can clean old logs or set up log rotation with `logrotate`.

---

## 14. How do you work with environment variables?

**Answer:**
Environment variables are key-value pairs available to all processes:

- `echo $HOME` — print a variable
- `echo $PATH` — shows directories where the system looks for executables
- `export MY_VAR="hello"` — set a variable for the current session and child processes
- `env` or `printenv` — list all environment variables
- `unset MY_VAR` — remove a variable

To make variables **permanent**, add them to:
- `~/.bashrc` or `~/.bash_profile` — for the current user
- `/etc/environment` or `/etc/profile` — system-wide

After editing, run `source ~/.bashrc` to reload without logging out.

The most important variable is `PATH` — it defines where Linux looks for commands. If I install something and the command isn't found, it's usually a PATH issue.

---

## 15. What network-related commands do you know?

**Answer:**
- `ping google.com` — test if a host is reachable
- `curl https://api.example.com/users` — make HTTP requests from the command line. `curl -X POST -d '{"name":"test"}' -H "Content-Type: application/json" url` for POST requests.
- `wget https://example.com/file.zip` — download a file
- `netstat -tuln` or `ss -tuln` — show listening ports
- `ip addr` or `ifconfig` — show network interfaces and IP addresses
- `ssh user@host` — remote login to another machine
- `scp file.txt user@host:/path` — securely copy files to remote machine
- `nslookup domain.com` or `dig domain.com` — DNS lookup

In troubleshooting, if my app isn't reachable, I check: Is it running (`ps`)? Is it listening on the right port (`netstat`)? Is the firewall blocking it (`iptables`)? Can I reach the host (`ping`/`curl`)?

---

## 16. What is the difference between a hard link and a soft (symbolic) link?

**Answer:**
- **Hard link** (`ln file.txt hardlink.txt`) — creates another name pointing to the **same data** on disk (same inode). Deleting the original file doesn't affect the hard link. Cannot cross filesystems. Cannot link directories.

- **Soft/Symbolic link** (`ln -s file.txt symlink.txt`) — creates a pointer to the **file path** (like a shortcut). If the original file is deleted, the symlink becomes **broken**. Can cross filesystems. Can link directories.

In practice, I use **symbolic links** much more often. For example, `ln -s /opt/jdk-17 /usr/local/java` so that `/usr/local/java` always points to the current version. When I upgrade, I just change where the symlink points.

---

## 17. How do you schedule tasks in Linux?

**Answer:**
Using **cron** — the built-in job scheduler:

`crontab -e` opens my cron file. Each line follows this format:
```
minute  hour  day  month  weekday  command
```

Examples:
- `0 2 * * * /home/kunal/backup.sh` — run backup at 2:00 AM every day
- `*/5 * * * * /scripts/health-check.sh` — run every 5 minutes
- `0 0 * * 0 /scripts/cleanup.sh` — run at midnight every Sunday

`crontab -l` lists my scheduled jobs. `crontab -r` removes all.

For one-time scheduled tasks, I use the `at` command: `echo "command" | at 3:00 PM`.

---

## 18. What is the `/etc/passwd` file?

**Answer:**
It's the **user account database**. Each line represents a user in this format:

`username:x:UID:GID:comment:home_directory:shell`

For example: `kunal:x:1000:1000:Kunal:/home/kunal:/bin/bash`

- `x` means the password is stored in `/etc/shadow` (encrypted)
- UID 0 is root, UIDs 1-999 are system accounts, 1000+ are regular users
- The shell field shows what shell the user gets at login

To add a user: `sudo useradd -m -s /bin/bash kunal` then `sudo passwd kunal`. To delete: `sudo userdel -r kunal`.

---

## 19. How do you troubleshoot a service that's not running?

**Answer:**
My approach is systematic:

1. **Check if the service is running:** `systemctl status servicename` — shows if it's active, failed, or inactive, plus recent logs.
2. **Check logs:** `journalctl -u servicename -n 50` — last 50 log lines for that service. Or check `/var/log/` for application-specific logs.
3. **Try to start it:** `sudo systemctl start servicename`
4. **If it fails, check port conflicts:** `netstat -tuln | grep PORT` — is something else using the same port?
5. **Check configuration:** Review the service's config file for errors.
6. **Check permissions:** Does the service user have access to the required files/directories?
7. **Check disk space:** `df -h` — a full disk can prevent services from starting.

---

## 20. What is the `chmod 777` and why should you avoid it?

**Answer:**
`chmod 777` gives **read, write, and execute permissions to EVERYONE** — owner, group, and others. It's a security risk because any user or process on the system can read, modify, or execute that file.

In production, this is almost never acceptable. Instead, I follow the **principle of least privilege** — give only the minimum permissions needed:
- Application files: `644` (owner reads/writes, everyone else reads)
- Scripts: `755` (owner full, everyone else read/execute)
- Sensitive configs: `600` (only owner reads/writes)
- Directories: `755`

If a teammate says "just chmod 777 it," I'd push back and figure out the right permissions for the use case.
