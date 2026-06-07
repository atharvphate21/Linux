These are the main Linux commands used to read, filter, and analyze logs.

## 1) `journalctl`

`journalctl` reads logs from **systemd journal**.
It is mainly used on modern Linux systems.

### Common commands

```bash
journalctl
journalctl -xe
journalctl -u nginx
journalctl -f
journalctl --since "1 hour ago"
journalctl --since today
journalctl -p err
```

### Meaning

* `journalctl` → show all journal logs
* `journalctl -xe` → recent logs with extra details, useful for troubleshooting
* `journalctl -u nginx` → logs of a specific service
* `journalctl -f` → follow logs live
* `journalctl --since "1 hour ago"` → logs from last 1 hour
* `journalctl -p err` → only error priority logs

### Example

```bash
journalctl -u ssh -f
```

This follows SSH service logs live.

---

## 2) `dmesg`

`dmesg` shows **kernel ring buffer** messages.
It is mainly used for:

* boot issues
* hardware issues
* driver problems
* disk/network/kernel messages

### Common commands

```bash
dmesg
dmesg | tail
dmesg | grep -i error
dmesg -T
```

### Meaning

* `dmesg` → kernel messages
* `dmesg | tail` → latest kernel logs
* `dmesg | grep -i error` → find errors
* `dmesg -T` → human-readable timestamps

### Example

```bash
dmesg -T | grep -i usb
```

Shows USB-related kernel messages.

---

## 3) `tail`

`tail` shows the last lines of a file.
Very useful for application logs.

### Common commands

```bash
tail /var/log/syslog
tail -n 50 /var/log/nginx/error.log
tail -f /var/log/messages
```

### Meaning

* `tail file` → last 10 lines
* `tail -n 50 file` → last 50 lines
* `tail -f file` → follow log live

### Example

```bash
tail -f /var/log/nginx/access.log
```

---

## 4) `grep`

`grep` searches for matching text patterns in logs.

### Common commands

```bash
grep "error" app.log
grep -i "failed" app.log
grep -n "timeout" app.log
grep -v "info" app.log
grep -E "error|fail|critical" app.log
```

### Meaning

* `grep "error"` → lines containing `error`
* `grep -i` → case-insensitive
* `grep -n` → show line numbers
* `grep -v` → exclude matching lines
* `grep -E` → multiple patterns using regex

### Example

```bash
grep -i "oom" /var/log/syslog
```

---

## 5) `awk`

`awk` is used for **column-wise processing**, filtering, and reporting.

Logs often have space-separated fields, so `awk` helps extract specific parts.

### Common commands

```bash
awk '{print $1,$2,$3}' app.log
awk '/error/ {print $0}' app.log
awk '{print $NF}' app.log
```

### Meaning

* `$1`, `$2`, `$3` → first, second, third columns
* `$0` → entire line
* `$NF` → last field

### Example

Suppose log:

```bash
2026-04-05 10:00:01 ERROR Database connection failed
```

Command:

```bash
awk '{print $3,$4,$5}' app.log
```

Output:

```bash
ERROR Database connection
```

### Useful example

Count requests by IP from access log:

```bash
awk '{print $1}' access.log | sort | uniq -c | sort -nr
```

---

## 6) `sed`

`sed` is used for **search, replace, and stream editing**.

### Common commands

```bash
sed -n '1,10p' app.log
sed -n '/ERROR/p' app.log
sed 's/error/ERROR/g' app.log
```

### Meaning

* `sed -n '1,10p'` → print lines 1 to 10
* `sed -n '/ERROR/p'` → print lines matching ERROR
* `sed 's/error/ERROR/g'` → replace text

### Example

```bash
sed -n '/failed/p' /var/log/auth.log
```

---

# Combined real-world usage

## Check failed SSH login attempts

```bash
grep "Failed password" /var/log/auth.log
```

Or:

```bash
journalctl -u ssh | grep "Failed password"
```

---

## Follow service logs live

```bash
journalctl -u nginx -f
```

---

## Check latest kernel disk errors

```bash
dmesg -T | grep -i disk
```

---

## Watch app log and filter errors

```bash
tail -f app.log | grep -i error
```

---

## Print only first column from access log

```bash
awk '{print $1}' access.log
```

---

## Print lines containing 500 status code

```bash
awk '$9 == 500 {print $0}' access.log
```

This is common in nginx/apache logs where field 9 is status code.

---

## Show only lines from a date pattern

```bash
grep "2026-04-05" app.log
```

---

## Replace IP with masked value for review

```bash
sed 's/192\.168\.1\.[0-9]*/192.168.1.xxx/g' app.log
```

---

# Quick difference

## `journalctl`

For systemd service and system logs.

## `dmesg`

For kernel and hardware logs.

## `tail`

For last lines and live monitoring.

## `grep`

For searching patterns.

## `awk`

For column extraction and simple analysis.

## `sed`

For editing/filtering text streams.

---

# Most important commands for troubleshooting

```bash
journalctl -xe
journalctl -u nginx -f
dmesg -T | tail
tail -f /var/log/syslog
grep -i error app.log
awk '{print $1}' access.log | sort | uniq -c | sort -nr
sed -n '/ERROR/p' app.log
```

---

# Interview-style summary

* `journalctl` is used to view logs stored by systemd.
* `dmesg` is used to inspect kernel and hardware-related messages.
* `tail` is used to see the last lines of a log file and monitor logs in real time.
* `grep` is used to search specific text patterns.
* `awk` is used to extract columns and process structured log lines.
* `sed` is used to print, replace, and transform text in streams.

---

# Very simple memory trick

* **journalctl** → service logs
* **dmesg** → kernel logs
* **tail** → latest lines
* **grep** → find text
* **awk** → pick fields
* **sed** → edit text
