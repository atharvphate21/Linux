In Linux, **services** are background programs that keep running to do system work, like web servers, SSH, Docker, cron, or databases.

`systemctl` is the command used to manage those services on systems that use **systemd**.

## What is a service

A service is a long-running process, usually started during boot or when needed.

Examples:

* `sshd` → handles SSH connections
* `nginx` → web server
* `docker` → container engine
* `cron` or `crond` → scheduled tasks

These usually run in the background and do not need direct user interaction.

## What is systemctl

`systemctl` is the main command-line tool for:

* starting services
* stopping services
* restarting services
* checking service status
* enabling services at boot
* disabling services at boot

## Common systemctl commands

### 1. Check service status

```bash
systemctl status nginx
```

This shows:

* whether service is active or inactive
* main PID
* recent logs
* whether it failed

### 2. Start a service

```bash
sudo systemctl start nginx
```

Starts the service now.

### 3. Stop a service

```bash
sudo systemctl stop nginx
```

Stops the service.

### 4. Restart a service

```bash
sudo systemctl restart nginx
```

Useful after config changes.

### 5. Reload a service

```bash
sudo systemctl reload nginx
```

Reloads configuration without fully stopping the service, if supported.

### 6. Enable service at boot

```bash
sudo systemctl enable nginx
```

This makes the service start automatically when system boots.

### 7. Disable service at boot

```bash
sudo systemctl disable nginx
```

Prevents auto-start on boot.

### 8. Check if service is enabled

```bash
systemctl is-enabled nginx
```

### 9. Check if service is active

```bash
systemctl is-active nginx
```

## Service unit files

In systemd, services are defined by **unit files**, usually ending in `.service`.

Example:

* `nginx.service`
* `sshd.service`
* `docker.service`

You can refer to them with or without `.service`:

```bash
systemctl status nginx
systemctl status nginx.service
```

Both usually work.

## List services

### All services

```bash
systemctl list-units --type=service
```

### All service files

```bash
systemctl list-unit-files --type=service
```

## Important terms

### active

Service is running.

### inactive

Service is not running.

### enabled

Service will start automatically at boot.

### disabled

Service will not start automatically at boot.

### failed

Service tried to start but crashed or exited with error.

## Example

Suppose SSH is not working. You can check:

```bash
systemctl status ssh
```

If stopped, start it:

```bash
sudo systemctl start ssh
```

To make sure it starts after reboot:

```bash
sudo systemctl enable ssh
```

## View logs of a service

With systemd, logs can be checked using:

```bash
journalctl -u nginx
```

Follow live logs:

```bash
journalctl -u nginx -f
```

## Difference between service and systemctl

Older Linux systems used the `service` command:

```bash
service nginx start
```

Modern systems mostly use:

```bash
systemctl start nginx
```

`systemctl` is more powerful and is standard on systemd-based distributions like:

* Ubuntu
* CentOS 7+
* RHEL 7+
* Debian newer versions

## Interview-style summary

* A **service** is a background process that performs system or application tasks.
* `systemctl` is used to manage services in Linux systems using **systemd**.
* Main operations are:

  * `start`
  * `stop`
  * `restart`
  * `reload`
  * `status`
  * `enable`
  * `disable`

## Most used commands

```bash
systemctl status nginx
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl enable nginx
systemctl disable nginx
journalctl -u nginx
```
