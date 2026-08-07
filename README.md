# Redundant Enterprise Campus Network

## Overview

This project is a highly available enterprise campus network built in GNS3 utilizing Cisco multilayer switching and FortiGate firewalls.

The environment implements:

- Dual Core Layer 3 Switching
- Dual Access Layer Switching
- FortiGate Active-Passive High Availability
- OSPF Dynamic Routing
- HSRP First-Hop Redundancy
- EtherChannel (LACP)
- Dedicated OSPF Transit Network
- Dual WAN Connectivity
- DHCP Relay Services
- Loopback-Based Monitoring and Testing

---

# Device Inventory

## Core Layer

- MLS-CORE-A
- MLS-CORE-B

## Access Layer

- ACC-A
- ACC-B

## Firewall Layer

- FG-FW-A
- FG-FW-B

## WAN Edge

- ISP-DIA-DEMARC
- ISP-BB-DEMARC

## Endpoints

- HOST-A
- HOST-B
- HOST-C
- NMS-SERVER

---

# VLAN Inventory

| VLAN | Name | Subnet |
|--------|--------|--------|
| 10 | MGMT | 10.42.10.0/28 |
| 20 | USERS | 10.42.20.0/24 |
| 30 | WIFI | 10.42.30.0/24 |
| 40 | GUEST | 10.42.40.0/24 |
| 100 | FINANCE | 10.42.100.0/24 |
| 999 | OSPF_TRANSIT | 10.42.254.0/30 |

---

# Core Infrastructure

## Core Interconnect

Port-Channel1

Protocol:

- LACP

Allowed VLANs:

- 10
- 20
- 30
- 40
- 100
- 999

---

# HSRP

## Virtual Gateways

| VLAN | Virtual IP |
|--------|--------|
| 10 | 10.42.10.1 |
| 20 | 10.42.20.1 |
| 30 | 10.42.30.1 |
| 40 | 10.42.40.1 |
| 100 | 10.42.100.1 |

### MLS-CORE-A Active

- VLAN 10
- VLAN 20
- VLAN 100

### MLS-CORE-B Active

- VLAN 30
- VLAN 40

---

# OSPF

## Process ID

42

## Area

0

## Router IDs

| Device | Router ID |
|----------|----------|
| MLS-CORE-A | 10.42.10.2 |
| MLS-CORE-B | 10.42.10.3 |
| FortiGate HA Cluster | 10.42.10.4 |

## OSPF Adjacencies

- MLS-CORE-A ↔ FG-FW-A
- MLS-CORE-B ↔ FG-FW-A
- MLS-CORE-A ↔ MLS-CORE-B (VLAN 999)

---

# OSPF Transit Network

## VLAN 999

Purpose:

- Core-to-Core OSPF Adjacency
- Alternate Routing Path
- Single-Link Failure Recovery

### Addressing

| Device | IP Address |
|----------|----------|
| MLS-CORE-A | 10.42.254.1/30 |
| MLS-CORE-B | 10.42.254.2/30 |

---

# Firewall Transit Networks

## FG-FW-A

| Interface | Address |
|------------|------------|
| port2 | 10.42.5.1/30 |
| port3 | 10.42.5.5/30 |

## FG-FW-B

| Interface | Address |
|------------|------------|
| port4 | 10.42.5.9/30 |
| port5 | 10.42.5.13/30 |

## MLS-CORE-A

| Interface | Address |
|------------|------------|
| Gi0/0 | 10.42.5.2/30 |
| Gi0/1 | 10.42.5.10/30 |

## MLS-CORE-B

| Interface | Address |
|------------|------------|
| Gi0/0 | 10.42.5.6/30 |
| Gi0/1 | 10.42.5.14/30 |

---

# FortiGate High Availability

Mode:

- Active-Passive

Heartbeat Interfaces:

- port6
- port7

Priorities:

- FG-FW-A: 200
- FG-FW-B: 100

Override:

- Enabled

Session Pickup:

- Enabled

---

# Loopback Interfaces

## FortiGate HA Cluster

| Interface | Address |
|----------|----------|
| Loopback0 | 10.42.255.1/32 |

Purpose:

- OSPF Advertisement
- Network Monitoring
- HA Validation
- Redundancy Testing

---

# WAN Connectivity

## ISP-DIA

### WAN Segment

172.16.0.0/29

| Device | Address |
|----------|----------|
| ISP-DIA-DEMARC | 172.16.0.1 |
| FortiGate port8 | 172.16.0.2 |

### Simulated Internet Services

- 8.8.8.8
- 8.8.4.4

---

## ISP-BB

### WAN Segment

172.16.0.8/29

| Device | Address |
|----------|----------|
| ISP-BB-DEMARC | 172.16.0.9 |
| FortiGate port9 | 172.16.0.10 |

### Simulated Internet Services

- 1.1.1.1
- 1.0.0.1

---

# WAN Routing

Primary Route:

- 0.0.0.0/0 → 172.16.0.1 (ISP-DIA)

Backup Route:

- 0.0.0.0/0 → 172.16.0.9 (ISP-BB)

OSPF Default Route Origination:

- Enabled

Metric Type:

- E2

Metric:

- 10

---

# Access Layer Management

| Device | Address |
|----------|----------|
| ACC-A | 10.42.10.11 |
| ACC-B | 10.42.10.12 |

---

# Management Devices

| Device | Address |
|----------|----------|
| NMS-SERVER | 10.42.10.6 |

---

# Routing Technologies

- OSPF
- HSRP
- Inter-VLAN Routing
- Static WAN Routing

---

# Layer 2 Technologies

- 802.1Q Trunking
- PVST
- EtherChannel
- LACP
- PortFast
- BPDU Guard

---

# High Availability Technologies

- FortiGate Active-Passive HA
- HSRP
- EtherChannel
- OSPF Redundancy
- Core-to-Core OSPF Transit Network
