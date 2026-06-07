**Ports** and **sockets** are basic networking concepts in Linux and computer networks.

## Port

A **port** is a logical number used to identify a specific service or application on a machine.

A computer has one IP address, but many applications can use the network at the same time.
Ports help the OS know **which application should receive the data**.

Examples:

* **22** → SSH
* **80** → HTTP
* **443** → HTTPS
* **3306** → MySQL

So:

* **IP address** identifies the machine
* **Port** identifies the service on that machine

Example:

```bash
192.168.1.10:80
```

This means:

* machine IP = `192.168.1.10`
* web service port = `80`

## Types of ports

### Well-known ports

Range: `0–1023`
Used by common services:

* 22 SSH
* 25 SMTP
* 53 DNS
* 80 HTTP
* 443 HTTPS

### Registered ports

Range: `1024–49151`

### Ephemeral / dynamic ports

Range: usually `49152–65535`
Temporary ports used by client applications.

Example:
When your browser connects to a website on port 443, your browser may use a random temporary local port like `52341`.

---

## Socket

A **socket** is an endpoint of communication between two systems or processes.

A socket is created using:

* IP address
* port number
* protocol (TCP/UDP)

A socket looks like:

```bash
IP:Port
```

Example:

```bash
192.168.1.10:80
```

But in real networking, one connection usually needs **two sockets**:

* source socket
* destination socket

Example:

```bash
Client:  192.168.1.5:52341
Server:  142.250.183.14:443
```

This full pair makes one network connection.

## Difference between port and socket

### Port

* Just a **number**
* Identifies a service on a host

### Socket

* Combination of **IP + Port + Protocol**
* Represents an actual communication endpoint

So:

* **Port** is like a room number
* **Socket** is like full address + room number

---

## TCP socket vs UDP socket

### TCP socket

* Connection-oriented
* Reliable
* Used in web, SSH, database connections

### UDP socket

* Connectionless
* Faster but less reliable
* Used in DNS, streaming, gaming, VoIP

---

## Real-world example

Suppose you open a website:

* Your browser uses local port `52341`
* Website server listens on port `443`

Connection:

```bash
Client socket:  10.0.0.2:52341
Server socket:  142.250.183.14:443
```

This allows your browser and the server to talk correctly.

---

## In Linux, how to check ports and sockets

### Show listening ports

```bash
ss -tuln
```

Meaning:

* `t` = TCP
* `u` = UDP
* `l` = listening
* `n` = numeric

### Show process using a port

```bash
ss -tulnp
```

or

```bash
netstat -tulnp
```

or

```bash
lsof -i :80
```

### Example

```bash
ss -tulnp | grep 22
```

Shows whether SSH is listening on port 22.

---

## Listening socket

When a server starts, it opens a **listening socket** on a port.

Example:

* Nginx listens on port 80
* SSH listens on port 22

That means the service is waiting for incoming client connections.

---

## Interview-style definition

**Port:** A logical communication number used by the OS to identify a specific network service on a host.

**Socket:** A communication endpoint formed by an IP address, port number, and protocol, used for sending and receiving data over the network.

---

## Very simple analogy

Think of a building:

* **IP address** = building address
* **Port** = room number
* **Socket** = complete communication endpoint to that room

---

## Short summary

* **Port** identifies a service
* **Socket** is the actual endpoint used for communication
* A service listens on a port using a socket
* Tools like `ss`, `netstat`, and `lsof` help check ports in Linux


