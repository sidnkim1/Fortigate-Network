# Redundant Enterprise Campus Network (GNS3)

## Overview

This project is a fully redundant enterprise campus network built in GNS3.

The design includes:

- Dual Layer-3 Core Switches
- Dual Access Switches
- FortiGate HA Firewall Cluster
- HSRP First-Hop Redundancy
- OSPF Dynamic Routing
- Layer-2 Redundancy with EtherChannel
- Management Network
- ISP Edge Connectivity
- Future NAT, VPN, and SD-WAN Integration

---

## Project Goals

### Primary Objectives

- Eliminate single points of failure
- Provide Layer-2 redundancy
- Provide Layer-3 redundancy
- Implement firewall high availability
- Implement dynamic routing via OSPF
- Simulate enterprise WAN connectivity

### Secondary Objectives

- Site-to-Site VPN
- SD-WAN
- Centralized Management
- Route Summarization
- Internet Edge Design

---

# Current Status

## Completed

- [x] VLAN Implementation
- [x] Access Layer Configuration
- [x] Core Layer Configuration
- [x] EtherChannel
- [x] HSRP
- [x] Inter-VLAN Routing
- [x] FortiGate HA
- [x] OSPF
- [x] OSPF Failover Testing
- [x] FortiGate Loopback Advertisement

## In Progress

- [ ] ISP Connectivity
- [ ] Default Route Injection
- [ ] NAT
- [ ] Internet Simulation

## Planned

- [ ] IPsec VPN
- [ ] SD-WAN
- [ ] Dual ISP Failover

---

# Topology

See:

/diagrams/topology.png

---

# Device Inventory

## Core Layer

| Device | Role |
|----------|----------|
| MLS-CORE-A | Primary Core |
| MLS-CORE-B | Secondary Core |

## Access Layer

| Device | Role |
|----------|----------|
| ACC-A | Access Switch |
| ACC-B | Access Switch |

## Firewall Layer

| Device | Role |
|----------|----------|
| FG-FW-A | HA Primary |
| FG-FW-B | HA Secondary |

## Servers

| Device | Role |
|----------|----------|
| NMS-SERVER | Management Host |

---

# VLANs

| VLAN | Name | Subnet |
|----------|----------|----------|
| 10 | MGMT | 10.42.10.0/28 |
| 20 | USERS | 10.42.20.0/24 |
| 30 | WIFI | 10.42.30.0/24 |
| 40 | GUEST | 10.42.40.0/24 |
| 100 | FINANCE | 10.42.100.0/24 |

---

# HSRP

## VLAN 10

VIP

10.42.10.1

MLS-CORE-A

10.42.10.2

MLS-CORE-B

10.42.10.3

---

# OSPF

## Process

42

## Area

0

## Router IDs

| Device | Router ID |
|----------|----------|
| MLS-CORE-A | 10.42.10.2 |
| MLS-CORE-B | 10.42.10.3 |
| FortiGate Cluster | 10.42.10.4 |

---

# Firewall Transit Networks

## Primary Paths

10.42.5.0/30

```text
FG-FW-A port2 = 10.42.5.1
MLS-CORE-A Gi0/0 = 10.42.5.2
