<img width="961" height="825" alt="image" src="https://github.com/user-attachments/assets/fd1705b8-200b-4d92-8fdd-784a694fda2b" />


# Enterprise WAN, Security, and SD-WAN Architecture Project

## Overview

This project simulates a multi-site enterprise network connected across a service provider WAN while implementing dynamic routing, Internet connectivity, network security, management plane protection, firewall high availability, and SD-WAN traffic engineering.

The environment was built entirely within GNS3 and combines enterprise networking, service provider networking, firewall administration, and WAN resiliency concepts into a single integrated design.

### Technologies Implemented

- OSPF
- EIGRP
- iBGP
- eBGP
- Route Reflection
- NAT/PAT
- ACLs
- SSH Management
- FortiGate HA
- FortiGate SD-WAN

---

## High-Level Architecture

### Headquarters

**CE-RTR-1**

```text
10.1.1.0/24   Management
10.1.2.0/24   Servers
```

### Branch A

**CE-RTR-2**

```text
10.2.1.0/24
10.2.2.0/24
```

### Branch B

**CE-RTR-3**

```text
10.3.1.0/24
10.3.2.0/24
```

### Branch C

**CE-RTR-4**

```text
10.4.1.0/24
10.4.2.0/24
```

All enterprise routes are exchanged dynamically through an EIGRP overlay running across the service provider WAN.

---

## Routing Architecture

### OSPF

Provides transport connectivity throughout the service provider infrastructure.

### BGP

Provides route transport within the simulated provider network using:

- iBGP
- eBGP
- Route Reflection

### EIGRP

Provides enterprise route exchange between headquarters and branch locations.

All branch LANs are learned dynamically through EIGRP.

---

## Service Provider WAN

The provider network consists of:

- Access Routers
- Backbone Routers
- Route Reflectors
- Provider Edge Services

The provider infrastructure transports enterprise traffic while maintaining dynamic routing and route scalability through BGP and Route Reflection.

---

## Internet Connectivity

Enterprise Internet access is provided using a layered PAT design.

### Branch PAT

Internal addresses:

```text
10.X.X.X
```

are translated to:

```text
198.51.100.X
```

at the CE routers.

### Internet Edge PAT

Provider WAN addresses are translated through the GNS3 Internet edge to provide access to the Internet.

This implementation provides Internet access for all enterprise locations while preserving enterprise routing functionality.

---

## Security Architecture

Access control policies were implemented on all branch routers.

### VPN Security Policy

Allowed:

- HTTPS
- SMTP
- SMTP Submission
- Management ICMP
- EIGRP Control Traffic

### WAN Security Policy

Allowed:

- HTTP
- HTTPS
- DNS
- ICMP
- BGP
- ISAKMP
- ESP

Denied:

- Telnet
- Unauthorized SSH
- Unapproved inbound traffic

ACL hit counters were used to validate policy enforcement.

---

## Management Plane Security

Dedicated management loopbacks were deployed on all branch routers.

### Management Addresses

```text
CE-RTR-2  10.2.99.1
CE-RTR-3  10.3.99.1
CE-RTR-4  10.4.99.1
```

### Management Security

Implemented controls include:

- SSH-only administration
- RSA key generation
- Local user authentication
- VTY access restrictions
- Management ACLs

Only authorized management networks are permitted administrative access.

---

## Firewall High Availability

A FortiGate HA pair protects the enterprise edge.

### Firewalls

```text
FG-FW-A
FG-FW-B
```

### Functions

- Stateful Firewalling
- NAT
- Internet Breakout
- SD-WAN
- Security Policy Enforcement

The HA implementation provides device redundancy and service continuity.

---

## Dual ISP Design

Two independent ISP connections provide Internet access.

### ISP-DIA-DEMARC

Primary Internet connection.

### ISP-BB-DEMARC

Secondary Internet connection.

Each ISP router maintains independent Internet connectivity through separate GNS3 cloud connections.

### WAN Networks

```text
172.16.0.0/29   DIA
172.16.0.8/29   BB
```

---

## SD-WAN Implementation

FortiGate SD-WAN was implemented to intelligently utilize both ISP circuits.

### WAN Members

```text
to_DIA
to_BB
```

### Performance SLA Monitoring

```text
DIA -> 8.8.8.8

BB  -> 1.1.1.1
```

Continuous SLA monitoring validates WAN availability and performance.

### Address Objects

FortiGate address objects were created to simplify policy management, SD-WAN rule creation, and future network segmentation.

Examples include:

- MGMT-NETWORK
- USERS-NETWORK
- WIFI-NETWORK
- GUEST-NETWORK
- FINANCE-NETWORK
- HQ-NETWORK
- ISP-DIA-GW
- ISP-BB-GW
- DIA-WAN
- BB-WAN

Address groups were used to organize enterprise resources and simplify policy administration.

### Traffic Steering

Destination-based SD-WAN policies were implemented to direct specific traffic across preferred ISP circuits.

Examples:

```text
9.9.9.9
→ ISP-DIA
```

```text
208.67.222.222
→ ISP-BB
```

Traffic steering was validated using:

- SD-WAN rule counters
- FortiGate traffic logs
- NAT translation verification
- End-to-end client connectivity testing

Traffic logs confirmed that packets were routed through the expected WAN interface and matched the intended SD-WAN policy.

### Automatic Failover

WAN failover was tested by intentionally disconnecting ISP connectivity.

Results:

```text
Preferred ISP Failure
        ↓
Traffic automatically migrated
to the remaining ISP.

Preferred ISP Restored
        ↓
Traffic automatically returned
to the preferred path.
```

---

## Technologies Implemented

### Routing

- OSPF
- EIGRP
- iBGP
- eBGP
- Route Reflection

### Internet Services

- NAT
- PAT
- Dual ISP Connectivity

### Security

- ACLs
- SSH
- FortiGate Firewall Policies

### High Availability

- FortiGate HA

### WAN Optimization

- SD-WAN
- Performance SLA Monitoring
- Traffic Steering
- Automatic Failover

### Management

- Loopback-Based Management
- Secure Administrative Access
- Management Plane Protection

---

## Project Outcome

The completed environment successfully demonstrates:

- Multi-site enterprise networking
- Service provider WAN architecture
- Dynamic routing
- Secure Internet access
- Management plane security
- Firewall high availability
- Dual ISP Internet resiliency
- Dual ISP active utilization
- Destination-based traffic steering
- Performance SLA monitoring
- SD-WAN traffic engineering
- Automatic WAN failover and recovery

The final design combines enterprise networking, service provider routing, network security, firewall high availability, and modern WAN optimization technologies into a single integrated lab environment.
