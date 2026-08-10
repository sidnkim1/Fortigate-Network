# FortiGate HA, Internet Edge, and SD-WAN Configuration

## High Availability

Navigate to:

```text
System
→ High Availability
```

Configure:

```text
Mode: Active-Passive

Group Name:
Campus-HA

Device Priority:
FG-FW-A = 200
FG-FW-B = 100

Heartbeat Interfaces:
port6
port7

Session Pickup:
Enabled

Override:
Enabled
```

---

## Core Transit Interfaces

Navigate to:

```text
Network
→ Interfaces
```

### port2

```text
Alias:
to_MLS-CORE-A

Address:
10.42.5.1/30

Administrative Access:
PING
```

### port3

```text
Alias:
to_MLS-CORE-B

Address:
10.42.5.5/30

Administrative Access:
PING
```

### port4

```text
Alias:
to_MLS-CORE-A-Backup

Address:
10.42.5.9/30

Administrative Access:
PING
```

### port5

```text
Alias:
to_MLS-CORE-B-Backup

Address:
10.42.5.13/30

Administrative Access:
PING
```

---

## Loopback Interface

Navigate to:

```text
Network
→ Interfaces
→ Create New
→ Interface
```

Configure:

```text
Type:
Loopback

Name:
Loopback0

Address:
10.42.255.1/32

Administrative Access:
PING
```

---

## OSPF

Navigate to:

```text
Network
→ OSPF
```

Configure:

```text
Router ID:
10.42.10.4

Area:
0.0.0.0
```

### Networks

```text
10.42.5.0/30
10.42.5.4/30
10.42.5.8/30
10.42.5.12/30
10.42.255.1/32
```

### Interfaces

```text
port2
port3
port4
port5
```

### Default Route Origination

```text
Default Information Originate:
Enabled

Metric Type:
Type 2

Metric:
10
```

---

## WAN Interfaces

Navigate to:

```text
Network
→ Interfaces
```

### port8 (ISP-DIA)

```text
Alias:
to_DIA

Role:
WAN

Address:
172.16.0.2/29

Administrative Access:
PING
HTTPS
SSH
```

### port9 (ISP-BB)

```text
Alias:
to_BB

Role:
WAN

Address:
172.16.0.10/29

Administrative Access:
PING
HTTPS
SSH
```

---

## Static Routes

Navigate to:

```text
Network
→ Static Routes
```

Create:

### DIA

```text
Destination:
0.0.0.0/0

Gateway:
172.16.0.1

Interface:
port8
```

### BB

```text
Destination:
0.0.0.0/0

Gateway:
172.16.0.9

Interface:
port9
```

---

# SD-WAN

Navigate to:

```text
Network
→ SD-WAN
```

Enable:

```text
SD-WAN
```

---

## Members

Add:

```text
to_DIA
Gateway:
172.16.0.1
```

```text
to_BB
Gateway:
172.16.0.9
```

---

## Performance SLA

### DIA_SLA

```text
Target:
8.8.8.8

Interface:
to_DIA
```

### BB_SLA

```text
Target:
1.1.1.1

Interface:
to_BB
```

---

## Address Objects

Navigate to:

```text
Policy & Objects
→ Addresses
```

Create:

```text
MGMT-NETWORK
USERS-NETWORK
WIFI-NETWORK
GUEST-NETWORK
FINANCE-NETWORK

HQ-NETWORK

DIA-WAN
BB-WAN

ISP-DIA-GW
ISP-BB-GW
```

---

## Traffic Steering Rules

Navigate to:

```text
Network
→ SD-WAN
→ SD-WAN Rules
```

### RuleA

```text
Destination:
9.9.9.9

Preferred Interface:
to_DIA
```

### RuleB

```text
Destination:
208.67.222.222

Preferred Interface:
to_BB
```

---

# Internet Access Policy

Navigate to:

```text
Policy & Objects
→ Firewall Policy
```

Create:

```text
Name:
HQ-Network -> SD-WAN

Incoming:
port2

Outgoing:
virtual-wan-link

Source:
HQ-NETWORK

Destination:
all

Service:
ALL

NAT:
Enabled
```

---

# Validation

## HA

```text
System
→ HA Status
```

Verify:

```text
FG-FW-A = Primary

FG-FW-B = Secondary
```

---

## OSPF

Verify OSPF neighbors are established with:

```text
MLS-CORE-A
MLS-CORE-B
```

---

## SD-WAN

Verify:

```text
Network
→ SD-WAN
→ Performance SLA
```

Both:

```text
DIA_SLA
BB_SLA
```

should display Healthy.

---

## Traffic Steering

Generate traffic to:

```text
9.9.9.9
208.67.222.222
```

Verify:

```text
Log & Report
→ Forward Traffic
```

Expected:

```text
9.9.9.9
→ RuleA
→ to_DIA
```

```text
208.67.222.222
→ RuleB
→ to_BB
```

---

## Failover

Disconnect DIA connectivity.

Verify:

```text
DIA_SLA
→ Down
```

Traffic should automatically migrate to:

```text
to_BB
```

Restore DIA and verify traffic returns to:

```text
to_DIA
```
