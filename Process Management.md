Process management in Linux is how the OS creates, schedules, monitors, and stops running programs.

A **process** is simply a running instance of a program. Every process has a unique **PID** (Process ID).

### Main concepts

**1. Process states**
A Linux process can be in different states, such as:

* **Running** – currently executing or ready to run
* **Sleeping** – waiting for an event or input
* **Stopped** – paused
* **Zombie** – finished execution but still has an entry in the process table until the parent collects it

**2. Foreground and background**

* **Foreground process** uses the terminal directly
* **Background process** runs without blocking the terminal

Example:

```bash
sleep 100 &
```

This runs in the background.

**3. Viewing processes**
Common commands:

```bash
ps
ps aux
top
htop
pstree
```

* `ps aux` shows all running processes
* `top` gives live process usage
* `htop` is a more user-friendly version if installed
* `pstree` shows parent-child relationships

**4. Process creation**
A new process is usually created using:

* `fork()` – creates a copy of the current process
* `exec()` – replaces the current process with a new program

In simple words:

* `fork` creates
* `exec` loads the new program

**5. Parent and child processes**
Every process usually has a **parent process**.
You can view parent-child relation using:

```bash
ps -ef
pstree
```

**6. Managing processes**
Useful commands:

```bash
kill PID
kill -9 PID
pkill process_name
nice -n 10 command
renice 5 -p PID
```

* `kill PID` sends a signal to a process
* `kill -9 PID` force kills it
* `pkill` kills by name
* `nice` starts a process with a priority
* `renice` changes priority of a running process

**7. Signals**
Signals are messages sent to processes.

Common ones:

* `SIGTERM` – politely ask process to stop
* `SIGKILL` – force stop
* `SIGSTOP` – pause process
* `SIGCONT` – continue paused process

Example:

```bash
kill -SIGTERM 1234
kill -9 1234
```

**8. Job control**
In terminal:

* `Ctrl + C` → stop process
* `Ctrl + Z` → suspend process
* `jobs` → show jobs
* `bg` → resume in background
* `fg` → bring to foreground

Example:

```bash
jobs
bg %1
fg %1
```

**9. Scheduling**
Linux CPU scheduler decides which process gets CPU time.
Important terms:

* **priority**
* **nice value**
* **time slice**

Nice values range from:

* `-20` = highest priority
* `19` = lowest priority

**10. Daemons**
A daemon is a background service process, like:

* `sshd`
* `systemd`
* `cron`

They usually start at boot and keep running in background.

### Example workflow

Check process:

```bash
ps aux | grep nginx
```

Kill process:

```bash
kill 1234
```

Force kill if needed:

```bash
kill -9 1234
```

Monitor live:

```bash
top
```

### Very short summary

Linux process management includes:

* creating processes
* tracking them with PID
* controlling foreground/background jobs
* sending signals
* changing priorities
* monitoring resource usage
