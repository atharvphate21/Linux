These are the main system resources you check in Linux to understand server health.

## CPU usage

CPU usage shows how much processor time is being used by running processes.

High CPU usually means:

* some process is consuming too much compute
* too many tasks are running
* the system may become slow

Useful commands:

```bash
top
htop
mpstat
vmstat
```

Check CPU details:

```bash
lscpu
```

In `top`, look at:

* `%us` → user CPU
* `%sy` → system CPU
* `%id` → idle CPU
* `%wa` → I/O wait

If idle is low and usage is high, CPU is busy.

---

## Memory usage

Memory usage shows how much RAM is being used.

If memory is full:

* system becomes slow
* swapping may happen
* applications may crash if memory is exhausted

Useful commands:

```bash
free -h
top
htop
vmstat
cat /proc/meminfo
```

Best quick check:

```bash
free -h
```

Important terms:

* **used** → memory currently used
* **free** → completely unused RAM
* **buff/cache** → memory used for cache, can be reused
* **available** → memory actually available for new programs

Focus more on **available** than just **free**.

---

## Disk usage

Disk usage shows how much storage space is used in filesystem.

If disk gets full:

* logs cannot be written
* apps may fail
* databases/services may stop working

Useful commands:

```bash
df -h
du -sh *
lsblk
```

### Check filesystem usage

```bash
df -h
```

Shows:

* total size
* used space
* available space
* mount point

### Check which directory is taking space

```bash
du -sh /var/*
du -sh /home/*
```

* `df -h` → filesystem level
* `du -sh` → directory/file level

---

## Inode usage

An **inode** stores metadata about a file, like:

* ownership
* permissions
* size
* timestamps
* disk block locations

Each file uses one inode.

A system can run out of inodes even if disk space is still available.
This happens when there are too many small files.

If inode usage becomes 100%:

* you cannot create new files
* services may fail
* logs may stop

Check inode usage:

```bash
df -i
```

This shows:

* total inodes
* used inodes
* free inodes
* inode usage percentage

---

## Commands summary

### CPU

```bash
top
htop
mpstat
lscpu
```

### Memory

```bash
free -h
top
cat /proc/meminfo
```

### Disk

```bash
df -h
du -sh /path/*
lsblk
```

### Inodes

```bash
df -i
```

---

## Real-world understanding

### 1. High CPU

A process like Java, Python, Docker container, or database may consume too much CPU.

Check:

```bash
top
ps aux --sort=-%cpu | head
```

### 2. High memory

A process may have memory leak or many applications may be running.

Check:

```bash
free -h
ps aux --sort=-%mem | head
```

### 3. Disk full

Large logs, backups, Docker images, temp files, or old packages may fill disk.

Check:

```bash
df -h
du -sh /var/log/*
```

### 4. Inode full

Millions of tiny files can exhaust inodes.

Check:

```bash
df -i
find / -xdev -type f | wc -l
```

---

## Interview-style explanation

* **CPU usage** tells how busy the processor is.
* **Memory usage** tells how much RAM is consumed and how much is available.
* **Disk usage** tells how much storage space is used.
* **Inode usage** tells how many file metadata entries are consumed.

A Linux admin should monitor all four because system issues may happen even if only one of them is exhausted.

---

## Very short difference

* **CPU** = processing power
* **Memory** = temporary working space
* **Disk** = permanent storage
* **Inode** = file count metadata structure

---

## Most important commands for quick troubleshooting

```bash
top
free -h
df -h
df -i
ps aux --sort=-%cpu | head
ps aux --sort=-%mem | head
```
