# Redundant Enterprise Campus Network

## Overview

This project is a highly available enterprise campus network built in GNS3.

Implemented technologies include:

- Layer 2 Switching
- VLAN Segmentation
- EtherChannel (LACP)
- HSRP
- OSPF
- FortiGate Active-Passive High Availability
- Core-to-Core Dynamic Routing
- Inter-VLAN Routing
- Network Monitoring

---

# Device Inventory

## Core Layer

- MLS-CORE-A
- MLS-CORE-B

## Access Layer

- ACC-A
- ACC-B

## Security Layer

- FG-FW-A
- FG-FW-B

## End Devices

- HOST-A
- HOST-B
- HOST-C
- NMS-SERVER

---

# VLAN Inventory

| VLAN | Name | Network |
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

| VLAN | Gateway |
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

---

# Core-to-Core OSPF Transit

| Device | Address |
|----------|----------|
| MLS-CORE-A | 10.42.254.1/30 |
| MLS-CORE-B | 10.42.254.2/30 |

Purpose:

- Core-to-Core OSPF Adjacency
- Alternate Routing Path
- Single-Link Failure Recovery

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
| port2 | 10.42.5.9/30 |
| port3 | 10.42.5.13/30 |

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

# FortiGate HA

Mode:

- Active-Passive

Heartbeat Interfaces:

- port6
- port7

Session Pickup:

- Enabled

Management Interface:

- port1

Device Priorities:

- FG-FW-A: 200
- FG-FW-B: 100

Monitored Interfaces:

- port2
- port3

---

# Loopback Interfaces

## FortiGate HA Cluster

| Interface | Address |
|-----------|-----------|
| Loopback0 | 10.42.255.1/32 |

Purpose:

- OSPF Advertisement
- Network Monitoring
- HA Validation
- Reachability Testing

---

# Management Addresses

| Device | Address |
|----------|----------|
| MLS-CORE-A | 10.42.10.2 |
| MLS-CORE-B | 10.42.10.3 |
| ACC-A | 10.42.10.11 |
| ACC-B | 10.42.10.12 |
| NMS-SERVER | 10.42.10.6 |

---

# Routing Technologies

- OSPF
- HSRP
- Inter-VLAN Routing

---

# Layer 2 Technologies

- 802.1Q Trunking
- Rapid PVST+
- EtherChannel
- LACP

---

# High Availability Technologies

- FortiGate Active-Passive HA
- HSRP
- EtherChannel
- Core-to-Core OSPF Redundancy

---

# Future Enhancements

- Dual ISP Connectivity
- NAT
- IPsec VPN
- SD-WAN
- Syslog
- SNMP Monitoring
- Internet Edge Simulation
