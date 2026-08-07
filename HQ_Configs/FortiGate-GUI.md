# FortiGate HA Cluster Configuration

## High Availability

Navigate to:

```text
System
→ High Availability
```

Configure the FortiGate appliances in Active-Passive mode.

```text
Mode: Active-Passive

Group Name: Campus-HA

Device Priority:
FG-FW-A = 200
FG-FW-B = 100

Heartbeat Interfaces:
port6
port7

Session Pickup: Enabled
```

Enable override to allow the higher-priority appliance to reclaim primary status after recovery.

---

## Core Transit Interfaces

Navigate to:

```text
Network
→ Interfaces
```

### port2

```text
Alias: to_MLS-CORE-A

IP Address:
10.42.5.1/30

Administrative Access:
PING
```

### port3

```text
Alias: to_MLS-CORE-B

IP Address:
10.42.5.5/30

Administrative Access:
PING
```

### port4

```text
Alias: to_MLS-CORE-A-Backup

IP Address:
10.42.5.9/30

Administrative Access:
PING
```

### port5

```text
Alias: to_MLS-CORE-B-Backup

IP Address:
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
Type: Loopback

Name: Loopback0

IP Address:
10.42.255.1/32

Administrative Access:
PING
```

---

# OSPF Configuration

Navigate to:

```text
Network
→ OSPF
```

Configure:

```text
Router ID:
10.42.10.4
```

Create:

```text
Area 0.0.0.0
```

---

## OSPF Networks

Add the following networks:

```text
10.42.5.0/30
10.42.5.4/30
10.42.5.8/30
10.42.5.12/30
10.42.255.1/32
```

---

## OSPF Interfaces

Add:

```text
port2
port3
port4
port5
```

Area:

```text
0.0.0.0
```

---

## Default Route Origination

Navigate to:

```text
Network
→ OSPF
→ Advanced
```

Configure:

```text
Default Information Originate:
Regular Areas

Metric Type:
Type 2

Metric:
10
```

---

# WAN Interface Configuration

Navigate to:

```text
Network
→ Interfaces
```

## ISP-DIA

### port8

```text
Alias: ISP-DIA

Role: WAN

IP Address:
172.16.0.2/29

Administrative Access:
PING
HTTPS
SSH
```

---

## ISP-BB

### port9

```text
Alias: ISP-BB

Role: WAN

IP Address:
172.16.0.10/29

Administrative Access:
PING
HTTPS
SSH
```

---

# Static Routes

Navigate to:

```text
Network
→ Static Routes
```

## Primary Route

```text
Destination:
0.0.0.0/0

Gateway:
172.16.0.1

Interface:
port8

Administrative Distance:
10
```

---

## Secondary Route

```text
Destination:
0.0.0.0/0

Gateway:
172.16.0.9

Interface:
port9

Administrative Distance:
20
```

---

# Local-In Policies

Navigate to:

```text
Policy & Objects
→ Local In Policy
```

## Policy 1

```text
Incoming Interface:
port2

Source:
all

Destination:
all

Service:
PING

Action:
ACCEPT
```

## Policy 2

```text
Incoming Interface:
port3

Source:
all

Destination:
all

Service:
PING

Action:
ACCEPT
```

These policies allow external management devices to successfully ping Loopback0 (10.42.255.1).

---

# CLI Configuration

## HA Override

```bash
config system ha
 set override enable
end
```

This allows the higher-priority firewall to reclaim primary status following recovery.

---

# Validation Commands

## HA Status

```bash
get system ha status
```

## OSPF Neighbors

```bash
get router info ospf neighbor
```

## Routing Table

```bash
get router info routing-table all
```

## Interface Status

```bash
get system interface physical
```

## Ping Testing

```bash
execute ping 10.42.5.2
execute ping 10.42.5.6

execute ping 8.8.8.8
execute ping 8.8.4.4

execute ping 1.1.1.1
execute ping 1.0.0.1
```

## Loopback Troubleshooting

```bash
diagnose debug reset

diagnose debug flow filter daddr 10.42.255.1

diagnose debug flow trace start 20

diagnose debug enable
```

Disable debugging:

```bash
diagnose debug disable
```
