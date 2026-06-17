# Basic Linux Network Commands

## Purpose

This file documents basic Linux network commands used for server troubleshooting.

The goal is to understand how to check:

- Network interfaces
- IP addresses
- Routing table
- Listening ports
- DNS resolution
- Internet connectivity
- TCP port connectivity

## Security Note

All command outputs shown here are sanitized examples.

Do not publish real IP addresses, MAC addresses, hostnames, usernames, internal domains, or company network details.

---

## Commands Covered

| Command | Purpose |
|---|---|
| `ip addr` | Check network interfaces and IP addresses |
| `ip route` | Check routing table and default gateway |
| `ss -tulpen` | Check listening TCP/UDP ports |
| `ping` | Test basic connectivity |
| `dig` | Check DNS resolution |
| `curl -I` | Check HTTP/HTTPS response |
| `nc -vz` | Check TCP port connectivity |

---

## 1. ip addr

### Command

~~~bash
ip addr
~~~

### Purpose

Shows network interfaces and IP addresses configured on the Linux system.

### When to Use

Use this command when checking whether a server has an active network interface and a valid IP address.

### Sanitized Example

~~~text
$ ip addr

1: lo: <LOOPBACK,UP,LOWER_UP>
    inet 127.0.0.1/8 scope host lo

2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP>
    link/ether xx:xx:xx:xx:xx:xx
    inet 192.168.1.100/24 scope global eth0
~~~

### Explanation

- `lo` is the loopback interface used internally by the server.
- `eth0` is the main network interface.
- `inet` shows the IPv4 address.
- `UP` means the interface is enabled.
- `/24` is the subnet prefix.

### Troubleshooting Note

If there is no `inet` IPv4 address, the server may not have a valid IP configuration.

---

## 2. ip route

### Command

~~~bash
ip route
~~~

### Purpose

Shows the routing table of the Linux system.

### When to Use

Use this command when checking how traffic leaves the server and whether a default gateway exists.

### Sanitized Example

~~~text
$ ip route

default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.100
~~~

### Explanation

- `default via 192.168.1.1` means traffic to outside networks goes through gateway `192.168.1.1`.
- `dev eth0` means traffic uses the `eth0` interface.
- `192.168.1.0/24` is the local network route.

### Troubleshooting Note

If there is no `default` route, the server may not reach the internet or external networks.

---

## 3. ss -tulpen

### Command

~~~bash
ss -tulpen
~~~

### Purpose

Shows listening TCP and UDP ports with the related process.

### Flag Meaning

| Flag | Meaning |
|---|---|
| `-t` | Show TCP sockets |
| `-u` | Show UDP sockets |
| `-l` | Show listening ports |
| `-p` | Show process using the port |
| `-e` | Show extended information |
| `-n` | Show numeric IPs and ports |

### When to Use

Use this command when checking whether a service is listening on the expected port.

### Sanitized Example

~~~text
$ ss -tulpen

Netid State  Local Address:Port  Process
tcp   LISTEN 0.0.0.0:22          users:(("sshd",pid=1001,fd=3))
tcp   LISTEN 0.0.0.0:80          users:(("nginx",pid=1200,fd=6))
tcp   LISTEN 127.0.0.1:5432      users:(("postgres",pid=1300,fd=5))
udp   UNCONN 0.0.0.0:53          users:(("systemd-resolve",pid=900,fd=12))
~~~

### Explanation

- `0.0.0.0:22` means SSH is listening on all IPv4 interfaces.
- `0.0.0.0:80` means NGINX is listening on all IPv4 interfaces.
- `127.0.0.1:5432` means PostgreSQL is listening only locally.
- UDP port `53` is commonly used for DNS.

### Troubleshooting Note

If a service listens only on `127.0.0.1`, it cannot be accessed directly from another server.

---

## 4. ping

### Command

~~~bash
ping -c 4 8.8.8.8
ping -c 4 google.com
~~~

### Purpose

Tests basic network connectivity.

### When to Use

Use `ping` to check whether the server can reach another IP address or domain.

### Sanitized Example

~~~text
$ ping -c 4 8.8.8.8

4 packets transmitted, 4 received, 0% packet loss
~~~

### Explanation

- `ping 8.8.8.8` tests internet connectivity using an IP address.
- `ping google.com` tests internet connectivity using DNS name resolution.

### Troubleshooting Note

If ping to `8.8.8.8` works but ping to `google.com` fails, the issue is likely DNS.

---

## 5. dig

### Command

~~~bash
dig example.com
~~~

### Purpose

Checks DNS resolution.

### When to Use

Use `dig` when a domain name is not resolving.

### Sanitized Example

~~~text
$ dig example.com

;; ANSWER SECTION:
example.com.     300     IN      A       93.184.216.34
~~~

### Explanation

The domain `example.com` resolved to IP address `93.184.216.34`.

### Troubleshooting Note

If there is no answer section, check DNS configuration and resolver settings.

---

## 6. curl -I

### Command

~~~bash
curl -I https://example.com
~~~

### Purpose

Checks HTTP/HTTPS connectivity and response headers.

### When to Use

Use this command to verify whether a web service is reachable.

### Sanitized Example

~~~text
$ curl -I https://example.com

HTTP/2 200
content-type: text/html
~~~

### Explanation

`HTTP/2 200` means the web server responded successfully.

### Troubleshooting Note

If curl fails, check DNS, firewall, routing, TLS certificate, proxy settings, and service availability.

---

## 7. nc -vz

### Command

~~~bash
nc -vz example.com 443
~~~

### Purpose

Checks whether a TCP port is reachable.

### When to Use

Use this command when testing whether a remote service port is open.

### Sanitized Example

~~~text
$ nc -vz example.com 443

Connection to example.com 443 port [tcp/https] succeeded!
~~~

### Explanation

The server was able to connect to TCP port `443`.

### Troubleshooting Note

If the connection fails, check whether the service is running, firewall rules, security groups, and routing.

---

## Quick Troubleshooting Flow

### Server cannot reach internet

Run:

~~~bash
ip addr
ip route
ping -c 4 8.8.8.8
ping -c 4 google.com
dig google.com
~~~

Logic:

- If `ping 8.8.8.8` fails, check IP address, route, gateway, and firewall.
- If `ping 8.8.8.8` works but `ping google.com` fails, check DNS.

### Service is not accessible

Run:

~~~bash
ss -tulpen
curl -I http://localhost
nc -vz server-ip port
~~~

Check:

1. Is the service running?
2. Is it listening on the expected port?
3. Is it listening on `0.0.0.0` or only `127.0.0.1`?
4. Is the Linux firewall allowing it?
5. Is the cloud security group allowing it?

