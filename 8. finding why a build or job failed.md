To find why a **build** or **job** failed in Linux, CI/CD, or DevOps, the main goal is to trace:

1. **what failed**
2. **where it failed**
3. **why it failed**
4. **what changed**

## Simple approach

### 1. Read the exact error message

Start with the job output or build logs.

Look for words like:

* `error`
* `failed`
* `permission denied`
* `command not found`
* `no space left on device`
* `timeout`
* `segmentation fault`
* `exit code`

Useful commands:

```bash
grep -i "error\|fail\|exception" build.log
tail -n 100 build.log
less build.log
```

Do not just look at the last line. Many times the final failure is only a symptom. The real issue is a few lines above.

---

## 2. Check the exit code

A job usually fails because a command returned a non-zero exit code.

Example:

* `0` = success
* non-zero = failure

In shell:

```bash
echo $?
```

Common meanings:

* `1` = general failure
* `2` = misuse of shell command
* `126` = command found but not executable
* `127` = command not found
* `130` = terminated by Ctrl+C
* `137` = killed, often OOM or SIGKILL
* `143` = terminated by SIGTERM

`137` is very common in CI when the job is killed due to memory issue.

---

## 3. Find the failed stage or step

In CI/CD, first identify which step failed:

* checkout
* dependency install
* compile
* test
* package
* deploy

Example pipeline flow:

```bash
Checkout -> Build -> Test -> Package -> Deploy
```

If `Build` passed and `Test` failed, focus only there.

---

## 4. Check logs carefully

Use filtering tools:

```bash
grep -i "error" build.log
grep -i "failed" build.log
grep -i "denied" build.log
grep -i "not found" build.log
awk '/error|failed|exception/' build.log
```

For live jobs:

```bash
tail -f build.log
```

For systemd jobs/services:

```bash
journalctl -u service_name -xe
journalctl -u service_name --since "30 min ago"
```

---

## 5. Identify the failure type

Most build/job failures fall into a few categories.

### A. Syntax or code error

Examples:

* compilation error
* test assertion failed
* invalid YAML/JSON
* bad script syntax

Check:

```bash
python script.py
bash -n script.sh
yamllint file.yaml
```

---

### B. Dependency issue

Examples:

* package missing
* wrong version
* incompatible library
* lockfile mismatch

Messages:

* `module not found`
* `package does not exist`
* `version conflict`

Check:

```bash
pip install -r requirements.txt
npm install
mvn clean install
gradle build
```

---

### C. Permission issue

Examples:

* script not executable
* access denied to file or directory
* SSH key permission problem

Messages:

* `Permission denied`
* `EACCES`

Check:

```bash
ls -l
chmod +x script.sh
whoami
id
```

---

### D. Environment/config issue

Examples:

* missing env var
* wrong path
* wrong config file
* secret not available

Messages:

* `ENV_VAR not set`
* `file not found`
* `invalid configuration`

Check:

```bash
env | sort
cat .env
printenv
```

---

### E. Disk issue

Very common in build agents.

Messages:

* `No space left on device`

Check:

```bash
df -h
df -i
du -sh /tmp/*
```

---

### F. Memory issue

Messages:

* `Killed`
* exit code `137`
* Java heap space
* out of memory

Check:

```bash
free -h
dmesg -T | grep -i -E "killed process|oom|out of memory"
top
```

If kernel OOM killer stopped the job, `dmesg` often shows it.

---

### G. CPU or timeout issue

Messages:

* `job timed out`
* `context deadline exceeded`
* `operation timeout`

Check:

* system load
* stuck tests
* slow dependency download
* external API delays

Commands:

```bash
top
uptime
vmstat 1 5
```

---

### H. Network issue

Examples:

* cannot download dependencies
* DNS issue
* repo not reachable
* SSH connection failure

Messages:

* `connection refused`
* `could not resolve host`
* `network timeout`

Check:

```bash
ping host
curl -v URL
nslookup host
dig host
ssh -v user@host
```

---

### I. Service dependency issue

Maybe the build depends on:

* database
* Docker daemon
* Kubernetes cluster
* artifact repo
* SonarQube
* registry

Check related services:

```bash
systemctl status docker
systemctl status jenkins
journalctl -u docker -xe
```

---

## 6. Reproduce the failure

Try to run the same failing command manually.

Example:
Instead of only reading:

```bash
./build.sh
```

Run the exact failing step:

```bash
npm test
mvn test
terraform validate
docker build .
```

This helps isolate the real issue faster.

---

## 7. Compare with last successful build

Ask:

* what changed in code
* what changed in config
* what changed in dependencies
* what changed in infrastructure
* what changed in secrets or environment

Check:

* git diff
* new package versions
* new env vars
* changed permissions
* updated base image

Commands:

```bash
git log --oneline
git diff
git diff HEAD~1
```

A build that suddenly failed after a dependency or config change usually points to recent changes.

---

## 8. Check system logs if needed

If the job failure is not clear from app logs, check OS logs.

### Kernel logs

```bash
dmesg -T | tail -n 50
dmesg -T | grep -i error
```

### Service logs

```bash
journalctl -xe
journalctl -u jenkins -f
journalctl -u docker -f
```

---

## 9. Check artifacts and reports

Many CI jobs produce:

* test reports
* coverage reports
* junit xml
* core dumps
* screenshots
* lint output

Sometimes the console log only says:
`Tests failed`

But the detailed reason is in test reports.

---

## 10. Use a structured troubleshooting method

### Ask these in order:

* Which stage failed?
* What exact command failed?
* What was the exit code?
* What is the first real error in logs?
* Is it code, environment, permissions, disk, memory, network, or dependency?
* Did something change recently?
* Can I reproduce it manually?

---

# Common real examples

## Example 1: command not found

Log:

```bash
terraform: command not found
```

Cause:
Tool not installed or not in PATH.

Check:

```bash
which terraform
echo $PATH
```

---

## Example 2: permission denied

Log:

```bash
./deploy.sh: Permission denied
```

Fix:

```bash
chmod +x deploy.sh
```

---

## Example 3: no space left

Log:

```bash
write error: No space left on device
```

Check:

```bash
df -h
df -i
du -sh /var/log/*
```

---

## Example 4: job killed with 137

Log:

```bash
Killed
Process exited with code 137
```

Likely cause:
OOM kill.

Check:

```bash
free -h
dmesg -T | grep -i oom
```

---

## Example 5: tests failing

Log:

```bash
5 tests failed
```

Check:

* which test failed
* stack trace
* recent code changes
* environment dependency like DB or mock service

---

# Best commands to use

```bash
tail -n 100 build.log
grep -i "error\|fail\|exception\|denied\|killed" build.log
echo $?
df -h
df -i
free -h
top
dmesg -T | grep -i -E "oom|killed|error"
journalctl -xe
journalctl -u service_name
git diff
```

# Interview-style answer

To find why a build or job failed, I first identify the failed stage and exact command, then inspect logs for the first meaningful error instead of just the final line. After that, I verify exit codes and check common causes like dependency issues, syntax errors, permissions, missing environment variables, disk space, memory exhaustion, network failures, or service dependencies. If needed, I reproduce the failing command manually and compare it with the last successful build to isolate what changed.

# Very short summary

When a build/job fails:

* check the failed step
* read the exact logs
* find the first real error
* check exit code
* verify disk, memory, permissions, env vars, dependencies
* reproduce manually
* compare with last successful run

