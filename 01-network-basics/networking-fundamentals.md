# Networking Fundamentals

## Purpose

This document explains basic networking fundamentals used in Linux administration, cloud engineering, DevOps, and infrastructure troubleshooting.

This is not a deep networking guide. It covers only the important basics needed to understand how systems communicate in real server and cloud environments.

For deeper learning, refer to official documentation, networking books, vendor guides, and trusted technical websites.

---

## 1. What is a Network?

A network is a group of devices connected together so they can communicate.

Examples of network-connected devices:

- Laptop
- Server
- Router
- Firewall
- Load balancer
- Database server
- Cloud instance
- Mobile device

In infrastructure work, networking helps answer questions like:

- Can this server reach the internet?
- Can this application reach the database?
- Is the port open?
- Is DNS resolving correctly?
- Is traffic blocked by firewall or security group?
- Is the route correct?

---

## 2. Internet Basics

The internet is a large network of networks.

When a user opens a website, many things happen:

1. The domain name is resolved using DNS.
2. The browser finds the server IP address.
3. A connection is made to the server.
4. Data is sent and received using network protocols.
5. The website or API response is returned to the user.

Example:

~~~text
User Browser -> DNS -> Public IP -> Load Balancer -> Application Server -> Database
~~~

---

## 3. IP Address

An IP address identifies a device on a network.

Example IPv4 address:

~~~text
192.168.1.100
~~~

Common types:

| Type | Example | Use |
|---|---|---|
| Public IP | `8.8.8.8` | Reachable from the internet |
| Private IP | `192.168.1.100` | Used inside private networks |
| Loopback IP | `127.0.0.1` | Used by the machine to talk to itself |

Private IP ranges commonly include:

| Range | Example |
|---|---|
| `10.0.0.0/8` | `10.0.1.10` |
| `172.16.0.0/12` | `172.16.5.20` |
| `192.168.0.0/16` | `192.168.1.100` |

---

## 4. Subnet

A subnet is a smaller part of a larger network.

Example:

~~~text
192.168.1.0/24
~~~

This means the network range is usually:

~~~text
192.168.1.0 - 192.168.1.255
~~~

In simple terms:

- Network address: `192.168.1.0`
- Usable IPs: devices inside the subnet
- Broadcast address: `192.168.1.255`
- CIDR prefix: `/24`

In cloud environments, subnets are used to separate resources.

Example:

| Subnet Type | Purpose |
|---|---|
| Public subnet | Load balancer, bastion, public-facing resources |
| Private subnet | Application servers, databases, internal services |

---

## 5. Gateway

A gateway is a network device that allows traffic to leave one network and go to another network.

Example:

~~~text
default via 192.168.1.1
~~~

This means traffic going outside the local network goes through `192.168.1.1`.

In AWS, an Internet Gateway allows public subnets to reach the internet.

---

## 6. DNS

DNS means Domain Name System.

It converts domain names into IP addresses.

Example:

~~~text
example.com -> 93.184.216.34
~~~

Useful commands:

~~~bash
dig example.com
nslookup example.com
~~~

Troubleshooting logic:

- If ping to IP works but domain fails, it is likely a DNS issue.
- If DNS resolves but connection fails, check routing, firewall, port, or service status.

---

## 7. Port

A port identifies a specific service running on a server.

Example:

| Service | Port |
|---|---|
| SSH | `22` |
| HTTP | `80` |
| HTTPS | `443` |
| DNS | `53` |
| PostgreSQL | `5432` |
| Redis | `6379` |

Example:

~~~text
192.168.1.100:443
~~~

This means the server IP is `192.168.1.100` and the service port is `443`.

---

## 8. TCP and UDP

TCP and UDP are transport layer protocols.

### TCP

TCP is connection-oriented and reliable.

It is used when data delivery must be accurate.

Examples:

- HTTPS
- SSH
- Database connections
- API communication

### UDP

UDP is connectionless and faster, but it does not guarantee delivery.

It is used when speed is more important than guaranteed delivery.

Examples:

- DNS
- Streaming
- Real-time communication

Simple difference:

| Protocol | Reliable | Connection | Common Use |
|---|---|---|---|
| TCP | Yes | Connection-based | Web, SSH, APIs, databases |
| UDP | No guarantee | Connectionless | DNS, streaming, real-time traffic |

---

## 9. OSI Model - Simple View

The OSI model explains how network communication is layered.

For basic troubleshooting, remember this simplified view:

| Layer | Name | Example |
|---|---|---|
| Layer 7 | Application | HTTP, HTTPS, DNS, SSH |
| Layer 6 | Presentation | TLS/SSL, encryption, data formatting |
| Layer 5 | Session | Session handling, connection state |
| Layer 4 | Transport | TCP, UDP, ports |
| Layer 3 | Network | IP address, routing |
| Layer 2 | Data Link | MAC address, switching |
| Layer 1 | Physical | Cable, wireless, hardware |

Practical troubleshooting mapping:

| Problem | Layer to Check |
|---|---|
| Website not loading | DNS, HTTP/HTTPS, application service |
| TLS/certificate issue | Presentation layer, certificate, encryption |
| Login/session keeps expiring | Session/application layer |
| Port not reachable | TCP/UDP, firewall, security group |
| Server cannot reach internet | IP, route, gateway |
| Server has no network | Interface, cable, Wi-Fi, NIC |
| Domain not resolving | DNS |

### Note

In real-world Linux, DevOps, and cloud troubleshooting, Layers 5 and 6 are often discussed together with Layer 7 because modern applications usually handle sessions, encryption, and formatting inside the application stack.

For example:

- HTTPS uses HTTP at Layer 7 with TLS encryption commonly mapped to Layer 6.
- Login sessions, cookies, and tokens are usually handled at the application/session level.
- TCP/UDP port checks are usually Layer 4.
- IP routing issues are Layer 3.

---

## 10. Firewall

A firewall controls allowed and blocked traffic.

Examples:

| Firewall Type | Example |
|---|---|
| Linux firewall | UFW, iptables, firewalld |
| Cloud firewall | AWS Security Group, NACL |
| Network firewall | Hardware or virtual firewall |

Firewall rules usually check:

- Source IP
- Destination IP
- Port
- Protocol
- Direction: inbound or outbound

---

## 11. VPC

VPC means Virtual Private Cloud.

A VPC is a private network inside a cloud provider like AWS.

In AWS, a VPC contains:

- Subnets
- Route tables
- Internet Gateway
- NAT Gateway
- Security Groups
- NACLs
- EC2 instances
- Load balancers
- Databases

Simple AWS flow:

~~~text
Internet -> Internet Gateway -> Public Subnet -> Load Balancer -> Private Subnet -> Application Server -> Database
~~~

---

## 12. Public Subnet and Private Subnet

### Public Subnet

A public subnet has a route to an Internet Gateway.

Used for:

- Load balancers
- Bastion hosts
- Public-facing resources

### Private Subnet

A private subnet does not directly expose resources to the internet.

Used for:

- Application servers
- Databases
- Internal services

Private servers usually access the internet through NAT Gateway or NAT Instance.

---

## 13. Load Balancer

A load balancer distributes traffic across multiple servers.

Common cloud load balancers:

| Type | Layer | Use |
|---|---|---|
| ALB | Layer 7 | HTTP/HTTPS apps |
| NLB | Layer 4 | TCP/UDP high-performance traffic |

Example:

~~~text
User -> Load Balancer -> App Server 1
                    -> App Server 2
                    -> App Server 3
~~~

Benefits:

- High availability
- Health checks
- Traffic distribution
- SSL/TLS termination
- Host/path-based routing for ALB

---

## 14. Basic Troubleshooting Mindset

When something is not reachable, check step by step.

### Server network check

~~~bash
ip addr
ip route
ping -c 4 8.8.8.8
dig example.com
~~~

### Service check

~~~bash
ss -tulpen
curl -I http://localhost
nc -vz server-ip port
~~~

### Questions to ask

1. Does the server have an IP address?
2. Does it have a default route?
3. Can it reach the gateway?
4. Can it reach the internet?
5. Is DNS working?
6. Is the service running?
7. Is the service listening on the expected port?
8. Is the firewall allowing traffic?
9. Is the cloud security group allowing traffic?
10. Is the load balancer health check passing?

---

## Summary

Networking basics are important for Linux administration, DevOps, cloud engineering, SRE, and security operations.

Important concepts to understand:

- IP address
- Subnet
- Gateway
- DNS
- Port
- TCP and UDP
- OSI layers
- Firewall
- VPC
- Public and private subnet
- Load balancer
- Routing

This document covers only the basic fundamentals. Networking is a large topic, and deeper learning should continue through official documentation, practical labs, and trusted technical resources.
