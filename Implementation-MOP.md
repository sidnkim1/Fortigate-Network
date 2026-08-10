# Method of Procedure (MOP)

# FortiGate High Availability, Dual ISP, and SD-WAN Deployment

---

# 1. Change Summary

## Objective

Deploy a resilient Internet edge architecture consisting of:

- FortiGate High Availability (HA)
- Dual ISP Internet connectivity
- Redundant WAN circuits
- SD-WAN traffic steering
- SLA-based path monitoring
- Automatic WAN failover and recovery

This change will introduce enterprise-grade Internet resiliency while preserving existing campus and routing functionality.

---

# 2. Business Justification

The existing environment provides internal routing and campus redundancy but relies on a single logical Internet path and lacks intelligent WAN traffic engineering.

This implementation provides:

- Improved Internet resiliency
- Automatic failover during ISP outages
- Dynamic path selection
- Enhanced operational visibility
- Reduced risk of Internet service interruption
- Foundation for future cloud and security services

---

# 3. Scope

## Systems Included

### Firewalls

- FG-FW-A
- FG-FW-B

### Core Infrastructure

- MLS-CORE-A
- MLS-CORE-B

### Internet Edge

- ISP-DIA-DEMARC
- ISP-BB-DEMARC

### Distribution Infrastructure

- SW-DIA
- SW-BB

### Endpoints Used for Validation

- NMS-SERVER

---

# 4. Prerequisites

Prior to implementation, verify:

## Campus Infrastructure

- Core switches operational
- HSRP functioning correctly
- OSPF neighborships established
- NMS server reachable

## Firewall Infrastructure

- FortiGate appliances deployed
- HA heartbeat interfaces connected
- Firewall synchronization healthy

## ISP Routers

- ISP-DIA-DEMARC reachable
- ISP-BB-DEMARC reachable
- Cloud connectivity available

---

# 5. Assumptions

The following infrastructure is already operational:

- Campus VLANs
- HSRP
- OSPF
- Core routing
- Access switch connectivity
- FortiGate transit networks

This MOP does not modify:

- Campus VLAN design
- Core switch routing design
- Service provider infrastructure
- Internal addressing plan

---

# 6. Implementation Plan

## Phase 1 - Deploy FortiGate HA

### Objectives

Configure FortiGate appliances as a redundant firewall cluster.

### Tasks

- Configure Active-Passive HA
- Configure heartbeat interfaces
- Enable session synchronization
- Configure HA priorities
- Enable override

### Expected Result

```text
FG-FW-A = Primary

FG-FW-B = Secondary
```

### Validation

Verify:

```text
System
→ High Availability
```

Confirm:

```text
Cluster Status: Healthy

FG-FW-A: Primary
FG-FW-B: Secondary
```

---

## Phase 2 - Deploy Firewall Transit Connectivity

### Objectives

Provide redundant routed connectivity between the FortiGate cluster and core switches.

### Tasks

Configure:

```text
FG-FW-A <-> MLS-CORE-A

FG-FW-A <-> MLS-CORE-B

FG-FW-B <-> MLS-CORE-A

FG-FW-B <-> MLS-CORE-B
```

### Validation

Verify:

```text
Ping Between:
- Firewalls
- Core Switches
```

Verify OSPF neighbor establishment.

---

## Phase 3 - Configure OSPF Integration

### Objectives

Integrate the firewall cluster into the existing routing infrastructure.

### Tasks

- Configure OSPF Area 0
- Advertise firewall loopback
- Establish OSPF adjacencies

### Validation

Verify:

```text
MLS-CORE-A adjacency established

MLS-CORE-B adjacency established
```

Confirm:

```text
10.42.255.1/32
```

is reachable throughout the environment.

---

## Phase 4 - Deploy Dual ISP Connectivity

### Objectives

Provide two independent Internet paths.

### DIA Deployment

Configure:

```text
ISP-DIA-DEMARC

172.16.0.1/29
```

Connect:

```text
ISP-DIA-DEMARC
↓
Cloud
```

### BB Deployment

Configure:

```text
ISP-BB-DEMARC

172.16.0.9/29
```

Connect:

```text
ISP-BB-DEMARC
↓
Cloud
```

### Validation

Verify on both ISP routers:

```text
Internet Reachability
```

Example:

```text
9.9.9.9
208.67.222.222
```

---

## Phase 5 - Configure Internet Edge Routing

### Objectives

Provide Internet access through both WAN circuits.

### Tasks

Configure WAN interfaces:

```text
port8 -> DIA

port9 -> BB
```

Configure default routes toward:

```text
172.16.0.1

172.16.0.9
```

### Validation

Verify:

```text
Routing Table
```

contains:

```text
0.0.0.0/0
```

through both providers.

---

## Phase 6 - Configure SD-WAN

### Objectives

Introduce intelligent WAN path selection.

### Tasks

Enable:

```text
SD-WAN
```

Configure Members:

```text
to_DIA

to_BB
```

### Validation

Verify:

```text
Both WAN members healthy
```

---

## Phase 7 - Configure SLA Monitoring

### Objectives

Continuously validate ISP health.

### DIA SLA

Target:

```text
8.8.8.8
```

### BB SLA

Target:

```text
1.1.1.1
```

### Validation

Verify:

```text
DIA_SLA = Healthy

BB_SLA = Healthy
```

---

## Phase 8 - Configure Traffic Steering

### Objectives

Direct traffic over preferred WAN circuits.

### RuleA

Destination:

```text
9.9.9.9
```

Preferred Path:

```text
ISP-DIA
```

### RuleB

Destination:

```text
208.67.222.222
```

Preferred Path:

```text
ISP-BB
```

### Validation

Generate traffic from:

```text
NMS-SERVER
```

Verify through FortiGate logs:

```text
9.9.9.9
→ DIA

208.67.222.222
→ BB
```

---

## Phase 9 - Configure Internet Access Policy

### Objectives

Allow internal users Internet access through the SD-WAN fabric.

### Tasks

Create policy:

```text
HQ-Network
↓
virtual-wan-link
```

Enable:

```text
NAT
```

### Validation

Verify:

```text
Internet Access
```

from internal systems.

---

## Phase 10 - Validate Failover

### Objectives

Validate automatic WAN migration.

### Test Procedure

Generate continuous traffic to:

```text
9.9.9.9
```

Disconnect:

```text
ISP-DIA
```

Confirm:

```text
DIA_SLA
→ Down
```

Verify traffic automatically transitions to:

```text
ISP-BB
```

Reconnect DIA.

Confirm traffic automatically returns to the preferred path.

---
