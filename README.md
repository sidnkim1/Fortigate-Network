<img width="843" height="791" alt="image" src="https://github.com/user-attachments/assets/52506ee7-2a9f-4de2-b035-dd8972231c40" />


# Fortigate Edge + Cisco LAN Network

## Project Overview

This project simulates a multi-site enterprise network connected across a service provider WAN while incorporating enterprise routing, VPN technologies, Internet access, security controls, management plane protections, and SD-WAN traffic engineering.

The environment was built in GNS3 and consists of four enterprise locations connected through a simulated ISP backbone. The design combines underlay routing, service provider routing, overlay VPN technologies, enterprise security controls, and dual-ISP Internet connectivity.

---

# High-Level Architecture

## Enterprise Sites

### Hub Site

CE-RTR-1

Networks:

- 10.1.1.0/24
- 10.1.2.0/24

### Spoke Site A

CE-RTR-2

Networks:

- 10.2.1.0/24
- 10.2.2.0/24

### Spoke Site B

CE-RTR-3

Networks:

- 10.3.1.0/24
- 10.3.2.0/24

### Spoke Site C

CE-RTR-4

Networks:

- 10.4.1.0/24
- 10.4.2.0/24

---

# Service Provider WAN

The enterprise WAN is transported across a simulated provider network consisting of:

- Access Routers
- Backbone Routers
- Route Reflectors
- ISP Edge Devices

Technologies implemented:

- OSPF Underlay
- iBGP
- eBGP
- Route Reflection

The provider network serves as the transport layer for enterprise VPN services.

---

# VPN Overlay

Secure site-to-site connectivity is provided through a hub-and-spoke VPN architecture.

Technologies implemented:

- IPsec
- GRE Tunnel Interfaces
- EIGRP Overlay Routing

Hub Router:

- CE-RTR-1

Spoke Routers:

- CE-RTR-2
- CE-RTR-3
- CE-RTR-4

All remote networks are learned dynamically through EIGRP operating across encrypted IPsec tunnels.

---

# Internet Connectivity

Enterprise hosts are provided Internet access using a layered NAT design.

## CE Router PAT

Internal Hosts

```text
10.X.X.X
     ↓
198.51.100.X
