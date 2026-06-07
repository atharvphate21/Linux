SSH stands for **Secure Shell**. It is used to **securely connect to another Linux/Unix machine remotely**.

It is commonly used for:

* logging into remote servers
* running commands remotely
* transferring files
* managing cloud servers

## Basic syntax

```bash
ssh username@server_ip
```

Example:

```bash
ssh ubuntu@192.168.1.10
```

This means:

* `ubuntu` = remote username
* `192.168.1.10` = server IP

---

## Why SSH is used

SSH provides:

* encrypted communication
* secure remote login
* secure command execution
* authentication using password or key pair

Without SSH, remote access would be unsafe.

---

## Default SSH port

SSH uses:

```bash
22
```

You can connect on a custom port like this:

```bash
ssh -p 2222 ubuntu@192.168.1.10
```

---

## How SSH works

When you run:

```bash
ssh user@server
```

SSH does these things:

1. connects to remote server
2. verifies server identity
3. authenticates user
4. creates encrypted session
5. gives remote shell access

---

## Authentication methods

### 1. Password authentication

You enter the remote user password.

Example:

```bash
ssh user@server_ip
```

Then type password.

### 2. Key-based authentication

More secure and commonly used in DevOps and cloud.

It uses:

* **private key** on your machine
* **public key** on remote server

Generate key pair:

```bash
ssh-keygen
```

This usually creates:

* `~/.ssh/id_rsa` or `~/.ssh/id_ed25519` → private key
* `~/.ssh/id_rsa.pub` or `~/.ssh/id_ed25519.pub` → public key

Copy public key to server:

```bash
ssh-copy-id user@server_ip
```

Then login without password:

```bash
ssh user@server_ip
```

---

## Important SSH files

### On client machine

* `~/.ssh/id_rsa` → private key
* `~/.ssh/id_rsa.pub` → public key
* `~/.ssh/known_hosts` → stores known server fingerprints
* `~/.ssh/config` → SSH client config

### On server

* `~/.ssh/authorized_keys` → allowed public keys
* `/etc/ssh/sshd_config` → SSH server config

---

## First-time connection message

When connecting first time, you may see host authenticity warning like:

```bash
The authenticity of host ... can't be established
```

If you trust the server, type:

```bash
yes
```

Then server fingerprint is stored in `known_hosts`.

---

## Common SSH commands

### Login to remote server

```bash
ssh user@host
```

### Connect with specific port

```bash
ssh -p 2222 user@host
```

### Connect with private key

```bash
ssh -i mykey.pem ubuntu@ec2-ip
```

### Run command remotely

```bash
ssh user@host "hostname"
```

### Verbose mode for debugging

```bash
ssh -v user@host
```

---

## SSH service in Linux

SSH server service is usually:

* `sshd`

Check status:

```bash
systemctl status ssh
```

or on some systems:

```bash
systemctl status sshd
```

Start service:

```bash
sudo systemctl start ssh
```

Enable at boot:

```bash
sudo systemctl enable ssh
```

---

## SSH file transfer tools

### SCP

Copy file securely:

```bash
scp file.txt user@host:/home/user/
```

Copy from remote to local:

```bash
scp user@host:/home/user/file.txt .
```

### Rsync over SSH

```bash
rsync -avz file.txt user@host:/home/user/
```

---

## Common SSH issues

### 1. Connection refused

Possible reasons:

* SSH service not running
* wrong IP or port
* firewall blocking port 22

### 2. Permission denied

Possible reasons:

* wrong username
* wrong password
* wrong key
* public key not in `authorized_keys`

### 3. Host key verification failed

Usually happens if server fingerprint changed.

---

## Basic security best practices

* use key-based authentication instead of password
* disable root login if not needed
* change default port only if required
* restrict SSH access using firewall/security groups
* keep private key secure
* set correct permissions on `.ssh` files

Example permissions:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/id_rsa
```

---

## Real-world example

Connect to AWS EC2:

```bash
ssh -i mykey.pem ubuntu@54.123.45.67
```

Connect to remote server on custom port:

```bash
ssh -p 2222 admin@10.0.0.5
```

Run remote command:

```bash
ssh ubuntu@server_ip "df -h"
```

---

## Interview-style answer

SSH is a secure protocol used for remote login and remote command execution over an encrypted connection. It typically runs on port 22 and supports password-based and key-based authentication. In Linux administration and DevOps, SSH is widely used for accessing remote servers, file transfers, and automation.

## Very short summary

* SSH = secure remote access
* default port = 22
* command:

```bash
ssh user@host
```

* supports password and key authentication
* key files are stored in `~/.ssh`

