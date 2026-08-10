<img width="843" height="791" alt="image" src="https://github.com/user-attachments/assets/52506ee7-2a9f-4de2-b035-dd8972231c40" />


# Enterprise Campus, Service Provider, Security, and SD-WAN Architecture Lab

## Executive Summary

This project simulates a multi-site enterprise connected through a service provider WAN while implementing enterprise-grade routing, VPN services, security controls, secure management, Internet connectivity, high availability, and SD-WAN traffic engineering.

The environment was built entirely within GNS3 and combines technologies commonly found in modern enterprise and service provider networks. The completed topology demonstrates underlay and overlay routing, encrypted site-to-site connectivity, Internet breakout, firewall-based security, dual ISP connectivity, secure management plane design, and WAN resiliency through SD-WAN.

The final solution integrates:

- Enterprise campus networking
- Service provider WAN transport
- Dynamic routing
- VPN technologies
- Network security controls
- Firewall high availability
- NAT/PAT
- Secure administrative access
- Dual ISP Internet connectivity
- SD-WAN traffic steering
- Automatic WAN failover

---

# Network Architecture

The environment is organized into several logical layers.

## Layer 1: Transport Underlay

The underlay network is responsible for providing basic IP connectivity throughout the service provider infrastructure.

### Technologies

- OSPF
- Routed point-to-point links
- Layer 3 backbone design

### Responsibilities

- Provider infrastructure reachability
- Backbone transport
- Route propagation between provider devices
- Transport for BGP sessions
- Transport for enterprise VPN traffic

The underlay is provider-facing and remains isolated from enterprise route advertisements.

---

## Layer 2: Service Provider Routing

The service provider network transports customer routes across the WAN.

### Technologies

- iBGP
- eBGP
- Route Reflection

### Components

- Access Routers
- Backbone Routers
- Route Reflectors
- Edge Routers

### Responsibilities

- Customer route transport
- BGP scalability
- Centralized route distribution
- Elimination of full-mesh iBGP requirements

---

## Layer 3: Enterprise VPN Overlay

Secure enterprise connectivity is delivered through an encrypted hub-and-spoke VPN overlay.

### Technologies

- GRE
- IPsec
- EIGRP

### Hub Site

```text
CE-RTR-1
```

### Spoke Sites

```text
CE-RTR-2
CE-RTR-3
CE-RTR-4
```

### Responsibilities

- Dynamic route exchange
- Secure site-to-site communications
- Enterprise route advertisement
- Overlay convergence
- Branch connectivity

All enterprise routes traverse encrypted IPsec tunnels.

---

# Enterprise Site Design

## Headquarters

### Router

```text
CE-RTR-1
```

### Networks

```text
10.1.1.0/24  Management Network
10.1.2.0/24  Server Network
```

The headquarters site hosts centralized management functions and shared enterprise services.

---

## Branch A

### Router

```text
CE-RTR-2
```

### Networks

```text
10.2.1.0/24
10.2.2.0/24
```

### Management Loopback

```text
10.2.99.1/32
```

---

## Branch B

### Router

```text
CE-RTR-3
```

### Networks

```text
10.3.1.0/24
10.3.2.0/24
```

### Management Loopback

```text
10.3.99.1/32
```

---

## Branch C

### Router

```text
CE-RTR-4
```

### Networks

```text
10.4.1.0/24
10.4.2.0/24
```

### Management Loopback

```text
10.4.99.1/32
```

---

# Routing Architecture

## OSPF Underlay

The provider infrastructure uses OSPF as the transport routing protocol.

### Functions

- Infrastructure reachability
- WAN transport
- Backbone convergence

Customer routes are not exchanged through OSPF.

---

## BGP Service Provider Layer

BGP is used within the provider network to transport customer routing information.

### Components

- iBGP
- eBGP
- Route Reflection

### Benefits

- WAN scalability
- Reduced administrative overhead
- Efficient route distribution
- Service provider route transport

---

## EIGRP Overlay

EIGRP operates inside the encrypted VPN overlay.

### Responsibilities

- Enterprise route exchange
- Dynamic branch reachability
- Hub-and-spoke route distribution
- Automatic convergence

All branch networks are learned dynamically through EIGRP.

---

# VPN Architecture

## GRE

GRE provides routed tunnel interfaces capable of transporting dynamic routing protocols.

### Functions

- Logical WAN overlay
- EIGRP transport
- Tunnel abstraction

---

## IPsec

IPsec provides encryption of all overlay traffic.

### Functions

- Confidentiality
- Integrity
- Authentication
- Secure WAN communications

All GRE traffic is protected by IPsec.

---

# Internet Connectivity

Enterprise Internet access is provided through a layered NAT design.

## Customer Edge PAT

Each CE router performs PAT using its WAN interface address.

### Translation

```text
10.X.X.X
    ↓
198.51.100.X
```

### Purpose

- Internet access
- Address conservation
- User Internet breakout

---

## Provider Edge PAT

Additional PAT occurs at the provider edge.

### Translation

```text
198.51.100.X
      ↓
192.168.122.X
```

### Purpose

- Connectivity to the GNS3 NAT cloud
- Public Internet access
- Return traffic handling

---

# Security Architecture

Access controls are implemented at all spoke locations.

## VPN Security Policy

Allowed:

```text
HTTPS (443)
SMTP (25)
SMTP Submission (587)
ICMP Management Traffic
EIGRP Control Plane Traffic
```

Denied:

```text
Unauthorized VPN Traffic
```

---

## WAN Security Policy

Allowed:

```text
HTTP
HTTPS
DNS
ICMP
ESP
ISAKMP
BGP
```

Denied:

```text
Telnet
Unauthorized SSH
Unapproved Inbound Services
```

---

## Validation

Security policies were validated using:

```text
ACL Hit Counters
Connectivity Testing
Traffic Analysis
Application Validation
```

---

# Management Plane Security

Dedicated management loopbacks were deployed on each branch router.

## Management Addresses

```text
CE-RTR-2  10.2.99.1
CE-RTR-3  10.3.99.1
CE-RTR-4  10.4.99.1
```

---

## SSH Security

Implemented:

```text
SSH
Local Authentication
RSA Keys
VTY Restrictions
Management ACLs
```

Only the management network is permitted SSH access.

```text
10.1.1.0/24
```

---

# Firewall Infrastructure

Internet edge security is provided by a FortiGate HA cluster.

## Firewalls

```text
FG-FW-A
FG-FW-B
```

### Functions

- Stateful Inspection
- NAT
- Internet Breakout
- Security Enforcement
- SD-WAN
- Traffic Steering

---

# Firewall High Availability

The FortiGates operate as a high availability pair.

### Benefits

- Device redundancy
- Reduced downtime
- Centralized policy enforcement
- Seamless failover

---

# Dual ISP Design

To improve Internet resiliency, two independent ISP paths were implemented.

## ISP-DIA-DEMARC

Primary dedicated Internet circuit.

### Connected Components

```text
Internet Cloud
ISP-DIA-DEMARC
SW-DIA
FortiGate HA
```

### WAN Network

```text
172.16.0.0/29
```

### Gateway

```text
172.16.0.1
```

---

## ISP-BB-DEMARC

Secondary broadband circuit.

### Connected Components

```text
Internet Cloud
ISP-BB-DEMARC
SW-BB
FortiGate HA
```

### WAN Network

```text
172.16.0.8/29
```

### Gateway

```text
172.16.0.9
```

---

# SD-WAN Architecture

FortiGate SD-WAN was implemented to intelligently utilize both ISP circuits.

## SD-WAN Members

### DIA

```text
to_DIA
Gateway: 172.16.0.1
```

### BB

```text
to_BB
Gateway: 172.16.0.9
```

---

## Performance SLA Monitoring

### DIA_SLA

Monitored Destination:

```text
8.8.8.8
```

Preferred Link:

```text
to_DIA
```

---

### BB_SLA

Monitored Destination:

```text
1.1.1.1
```

Preferred Link:

```text
to_BB
```

---

## Destination-Based Traffic Steering

Traffic steering policies were implemented to direct specific Internet destinations out specific ISP circuits.

### RuleA

Destination:

```text
9.9.9.9
```

Selected Interface:

```text
ISP-DIA
```

Purpose:

```text
Demonstrate traffic steering through DIA.
```

---

### RuleB

Destination:

```text
208.67.222.222
```

Selected Interface:

```text
ISP-BB
```

Purpose:

```text
Demonstrate traffic steering through BB.
```

---

## SD-WAN Validation

Validation was performed using:

- FortiGate SD-WAN Rule Counters
- Performance SLA Monitoring
- Traffic Logs
- NAT Translation Verification

Traffic logs confirmed:

```text
9.9.9.9
→ ISP-DIA

208.67.222.222
→ ISP-BB
```

---

# Automatic WAN Failover

Automatic failover was validated by intentionally disconnecting WAN connectivity.

### Failure Scenario
