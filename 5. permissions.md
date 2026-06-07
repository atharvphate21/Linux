In Linux, **permissions** control who can read, write, or execute a file or directory.

## 3 permission types

### 1. Read (`r`)

* For a **file**: can view contents
* For a **directory**: can list files inside

### 2. Write (`w`)

* For a **file**: can modify the file
* For a **directory**: can create, delete, or rename files inside

### 3. Execute (`x`)

* For a **file**: can run it as a program or script
* For a **directory**: can enter/access it using `cd`

---

## 3 user classes

Permissions are set for:

* **User (u)** → owner of the file
* **Group (g)** → users in the file’s group
* **Others (o)** → everyone else

So Linux checks permissions in this order:

```bash
user | group | others
```

---

## Example

```bash
-rwxr-xr--
```

Break it like this:

* `-` → regular file
* `rwx` → owner can read, write, execute
* `r-x` → group can read, execute
* `r--` → others can only read

---

## View permissions

```bash
ls -l
```

Example output:

```bash
-rw-r--r-- 1 user user 1200 Apr 5 file.txt
```

Meaning:

* owner: read + write
* group: read
* others: read

---

## Numeric permission method

Each permission has a value:

* `r = 4`
* `w = 2`
* `x = 1`

Add them:

* `7 = rwx`
* `6 = rw-`
* `5 = r-x`
* `4 = r--`

Example:

```bash
chmod 755 script.sh
```

Means:

* owner = 7 = rwx
* group = 5 = r-x
* others = 5 = r-x

---

## Change permissions

### Using symbolic mode

```bash
chmod u+x file.sh
chmod g-w file.txt
chmod o+r file.txt
chmod a+x script.sh
```

* `u` = user
* `g` = group
* `o` = others
* `a` = all

### Using numeric mode

```bash
chmod 644 file.txt
chmod 755 script.sh
chmod 700 private.sh
```

Common ones:

* `644` → file owner can read/write, others read only
* `755` → executable file/script
* `700` → private file for owner only

---

## Change ownership

```bash
chown user file.txt
chown user:group file.txt
```

Example:

```bash
sudo chown root:root file.txt
```

Change group only:

```bash
chgrp developers file.txt
```

---

## File vs directory permissions

This is very important.

### For files

* `r` → read content
* `w` → edit content
* `x` → run file

### For directories

* `r` → list names inside directory
* `w` → create/delete/rename entries
* `x` → enter/access directory

Example:
If a directory has `r` but not `x`, you may see names but cannot access files inside properly.

---

## Default permissions

When a new file or directory is created, Linux applies default permissions based on **umask**.

Check:

```bash
umask
```

Common defaults:

* files often start near `644`
* directories often start near `755`

---

## Special permissions

### 1. SUID

Runs file with owner’s privileges.

```bash
chmod u+s file
```

### 2. SGID

Runs with group privileges or makes new files inherit directory group.

```bash
chmod g+s dir
```

### 3. Sticky bit

Used on shared directories so users can delete only their own files.

```bash
chmod +t /shared_dir
```

Common example:

```bash
/tmp
```

---

## Important commands

```bash
ls -l
chmod 755 file.sh
chmod u+x file.sh
chown user:group file.txt
chgrp developers file.txt
umask
```

---

## Real-world examples

### Make script executable

```bash
chmod +x deploy.sh
./deploy.sh
```

### Give read/write to owner only

```bash
chmod 600 secrets.txt
```

### Private directory

```bash
chmod 700 myfolder
```

### Shared readable file

```bash
chmod 644 notes.txt
```

---

## Interview-style explanation

Linux permissions define access control for files and directories using:

* **read**
* **write**
* **execute**

These are assigned to:

* **owner**
* **group**
* **others**

Permissions can be changed using `chmod`, ownership using `chown`, and groups using `chgrp`.

---

## Very short summary

* `r` = read
* `w` = write
* `x` = execute
* `chmod` = change permissions
* `chown` = change owner
* `ls -l` = view permissions

Example:

```bash
chmod 755 script.sh
```

