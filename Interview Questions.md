
---

## 1. How do you check which process is using port 8080?

I usually use `ss`, `lsof`, or `netstat`.

```bash
ss -tulnp | grep 8080
```

or

```bash
lsof -i :8080
```

This shows the PID and process name listening on port 8080. If needed, I then inspect the process with:

```bash
ps -fp <PID>
```

In troubleshooting, I also check whether it is actually listening on `0.0.0.0:8080`, `127.0.0.1:8080`, or some specific interface, because that affects reachability.

---

## 2. A Jenkins agent is suddenly offline on Linux. How would you debug it?

First, I would verify whether the agent process is running on the machine.

```bash
ps -ef | grep -i jenkins
systemctl status jenkins
```

If the agent runs as a service, I would inspect service logs:

```bash
journalctl -u jenkins -xe
```

Then I would check:

* network connectivity between controller and agent
* Java availability and version
* disk space and inode usage
* CPU and memory pressure
* SSH connectivity if it is an SSH-based agent
* permissions on workspace and agent directories

I would also review the Jenkins controller logs and the specific node configuration to see whether the disconnect is caused by authentication, remoting failure, Java crash, or resource exhaustion. My goal is to isolate whether it is a process issue, connectivity issue, environment issue, or workspace issue.

---

## 3. How do you find the top CPU-consuming and memory-consuming processes?

For a quick live view, I use:

```bash
top
```

or `htop` if available.

For a sorted one-time view:

```bash
ps aux --sort=-%cpu | head
ps aux --sort=-%mem | head
```

That gives me the top CPU and memory consumers immediately. If memory usage is suspicious, I also check system-level memory with:

```bash
free -h
```

and if needed I correlate that with `top`, `vmstat`, and `dmesg` for OOM events.

---

## 4. What is the difference between a process and a thread?

A process is an independent running instance of a program with its own memory space and system resources. A thread is a lightweight execution unit inside a process.

So the key difference is:

* **processes** are isolated from each other
* **threads** share the same memory space within a process

Because threads share memory, they are faster to create and switch than processes, but they need synchronization to avoid race conditions. In practice, if one process crashes it usually does not directly crash another process, but a faulty thread can affect the whole process.

---

## 5. How would you debug “permission denied” in a build step?

I would start by identifying exactly which file, command, or path is causing the error from the build logs. Then I would verify:

* which user the build is running as
* file and directory permissions
* ownership
* execute bit on scripts
* parent directory permissions
* SELinux or mount restrictions if applicable

Commands I use are:

```bash
whoami
id
ls -l
namei -l <path>
```

If it is a script execution problem, I check whether the file has execute permission:

```bash
chmod +x script.sh
```

If it is accessing files or directories, I validate ownership and group permissions with `ls -l` and `chown/chgrp` if needed. I also check whether the workspace is mounted with `noexec`, because that can also produce permission denied even when file permissions look correct.

---

## 6. What is the difference between soft link and hard link?

A soft link, or symbolic link, is a separate file that points to another file by pathname. A hard link is another directory entry pointing to the same inode as the original file.

Main differences:

* a **soft link** can point across filesystems and to directories, but it breaks if the target is deleted
* a **hard link** points to the same inode, usually cannot cross filesystems, and still works as long as one hard link remains

I usually explain it like this: a soft link is like a shortcut, while a hard link is another name for the same underlying file data.

---

## 7. How do you inspect recent system logs for a failing service?

On a systemd-based Linux machine, I use `journalctl`.

```bash
journalctl -u <service-name> -xe
journalctl -u <service-name> --since "1 hour ago"
journalctl -u <service-name> -f
```

That helps me inspect recent failures, stack traces, restart attempts, and startup errors. If needed, I also check application-specific log files under `/var/log` and correlate timestamps with `systemctl status <service-name>`.

My focus is to find the first meaningful error, not just the final failure message.

---

## 8. What could cause disk space issues even when `df -h` looks acceptable?

A common reason is inode exhaustion. In that case, disk blocks may still be available, but the filesystem cannot create new files because all inodes are used.

I check that with:

```bash
df -i
```

Other causes include:

* deleted files still held open by running processes
* one specific mount point being full while another looks fine
* application quotas
* container or build-cache growth under a different path
* reserved filesystem space for root

If I suspect deleted-but-open files, I use:

```bash
lsof | grep deleted
```

So `df -h` alone is not always enough. I always validate both block usage and inode usage.

---

## 9. How do you debug a build that works on one machine but fails on another?

I compare the two environments systematically.

I usually check:

* OS and kernel version
* installed tool versions such as Java, Python, Maven, Node, Docker, Terraform
* environment variables
* permissions and user context
* network access and DNS resolution
* available disk, memory, and CPU
* dependency versions and lockfiles
* path differences
* locale or shell differences if relevant

Commands I typically use:

```bash
env
which <tool>
<tool> --version
df -h
free -h
uname -a
```

Then I rerun the exact failing command manually on both machines and compare outputs. In most cases, the issue comes from environment drift, missing dependencies, different permissions, or different runtime/tool versions.

---

## 10. What happens when inode usage is full?

When inode usage reaches 100%, the system cannot create any new files, even if disk space is still available.

That means operations like:

* creating temp files
* writing logs
* downloading artifacts
* unpacking packages
* starting services

may fail with errors such as “No space left on device.”

I check inode usage with:

```bash
df -i
```

This usually happens when a filesystem contains a huge number of very small files. The fix is to identify and remove unnecessary files, especially small temp, cache, or log files.

---

