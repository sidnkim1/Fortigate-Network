<img width="843" height="791" alt="image" src="https://github.com/user-attachments/assets/52506ee7-2a9f-4de2-b035-dd8972231c40" />


# Dual ISP Edge Connectivity and SD-WAN

## Overview

To improve Internet resiliency and demonstrate enterprise WAN design concepts, two independent ISP demarcation routers were deployed and connected to separate Internet uplinks through GNS3 cloud interfaces.

The FortiGate HA cluster was configured with SD-WAN to provide:

- Dual ISP Internet connectivity
- Application and destination-based traffic steering
- Performance SLA monitoring
- Automatic failover and recovery
- Centralized WAN policy control

## Physical Topology

Internet Cloud A
    |
ISP-DIA-DEMARC
    |
SW-DIA
    |
FortiGate HA

Internet Cloud B
    |
ISP-BB-DEMARC
    |
SW-BB
    |
FortiGate HA

## WAN Networks

### DIA ISP

| Device | Address |
|----------|----------|
| ISP-DIA-DEMARC | 172.16.0.1/29 |
| FortiGate DIA Interface | 172.16.0.2/29 |

### BB ISP

| Device | Address |
|----------|----------|
| ISP-BB-DEMARC | 172.16.0.9/29 |
| FortiGate BB Interface | 172.16.0.10/29 |

## ISP Loopback Services

To simulate common external Internet destinations, loopback interfaces were created on both ISP routers.

### ISP-DIA-DEMARC

- 8.8.8.8
- 8.8.4.4
- 1.1.1.1
- 1.0.0.1

### ISP-BB-DEMARC

- 8.8.8.8
- 8.8.4.4
- 1.1.1.1
- 1.0.0.1

These addresses were used to demonstrate destination-based SD-WAN path selection and failover behavior.

## SD-WAN Configuration

### SD-WAN Members

| Member | Interface | Gateway |
|----------|----------|----------|
| DIA | to_DIA (port8) | 172.16.0.1 |
| BB | to_BB (port9) | 172.16.0.9 |

### Performance SLA

#### DIA_SLA

Target:

8.8.8.8

Monitored Interface:

to_DIA

#### BB_SLA

Target:

1.1.1.1

Monitored Interface:

to_BB

Both SLA health checks continuously verify WAN availability and trigger failover when a path becomes unavailable.

## Destination-Based Traffic Steering

### RuleA

Preferred Link:

ISP-DIA

Destinations:

- 9.9.9.9

Purpose:

Direct selected destinations through the DIA connection.

### RuleB

Preferred Link:

ISP-BB

Destinations:

- 208.67.222.222

Purpose:

Direct selected destinations through the BB connection.

## Firewall Policy

A single Internet access policy was configured using the SD-WAN zone.

Source Interface:

port2

Destination Interface:

virtual-wan-link

Source:

HQ-Network

Destination:

all

Services:

ALL

NAT:

Enabled

The SD-WAN engine determines the proper WAN interface after the firewall policy is matched.

## Validation

### Internet Connectivity

Verified from:

- FortiGate WAN interfaces
- NMS Server
- ISP-DIA-DEMARC
- ISP-BB-DEMARC

Test Destinations:

- 9.9.9.9
- 208.67.222.222

### SD-WAN Rule Validation

Traffic logs confirmed:

#### RuleA

Destination:

9.9.9.9

Selected Interface:

to_DIA

#### RuleB

Destination:

208.67.222.222

Selected Interface:

to_BB

### Failover Validation

Test Procedure:

1. Generate continuous traffic to a monitored destination.
2. Disconnect DIA Internet connectivity.
3. Observe DIA_SLA failure.
4. Verify traffic automatically transitions to ISP-BB.
5. Restore DIA connectivity.
6. Verify traffic returns to preferred DIA path.

Result:

Automatic failover and recovery were successfully validated.

## Design Benefits

- Dual active Internet connectivity
- Destination-based path steering
- SLA-driven failover
- Automatic path recovery
- Improved WAN resiliency
- Simplified Internet policy management through SD-WAN
- Enterprise-grade Internet edge architecture
