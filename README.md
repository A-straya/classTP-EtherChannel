# classTP-EtherChannel

# Configuring EtherChannel — Cisco Networking Lab

<p align="center">
  <img src="https://img.shields.io/badge/Cisco-Networking-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white" alt="Cisco Networking">
  <img src="https://img.shields.io/badge/Cisco%20IOS-15.0(2)-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white" alt="Cisco IOS">
  <img src="https://img.shields.io/badge/CCNA-Lab-1BA0D7?style=for-the-badge" alt="CCNA Lab">
  <img src="https://img.shields.io/badge/EtherChannel-PAgP%20%7C%20LACP-333333?style=for-the-badge" alt="EtherChannel">
</p>

<p align="center">
  <strong>3.2.1.4 Lab — Configuring EtherChannel</strong><br>
  Cisco Networking Academy / CCNA Switching Lab
</p>

---

## Overview

This repository contains a Cisco networking lab focused on **EtherChannel configuration and verification** using **PAgP** and **LACP**.

EtherChannel, also known as link aggregation, combines multiple physical Ethernet links into a single logical link. This provides increased bandwidth and redundancy.

The lab uses three Cisco switches and three PCs to configure and verify:

* Basic switch configuration
* VLAN 10 — `Staff`
* VLAN 99 — `Management`
* PAgP between S1 and S3
* LACP between S1 and S2
* LACP between S2 and S3
* 802.1Q trunking
* Native VLAN configuration
* EtherChannel verification
* End-to-end connectivity

---

## Lab Information

| Category        | Details                            |
| --------------- | ---------------------------------- |
| Platform        | Cisco IOS                          |
| Switches        | Cisco Catalyst 2960                |
| IOS             | 15.0(2) LANBASEK9                  |
| Lab             | 3.2.1.4 — Configuring EtherChannel |
| Protocols       | PAgP, LACP                         |
| VLANs           | VLAN 10, VLAN 99                   |
| Trunking        | 802.1Q                             |
| Management VLAN | VLAN 99                            |
| Host VLAN       | VLAN 10                            |

The original Cisco lab specifies Catalyst 2960 switches running Cisco IOS 15.0(2), while noting that commands and output may vary with the hardware and IOS version.

---

## Network Topology

```text
                         +----------------+
                         |      S1        |
                         |  Cisco 2960    |
                         +-------+--------+
                                 |
                  +--------------+--------------+
                  |                             |
              Po1 | PAgP                    Po2 | LACP
                  |                             |
                  |                             |
          +-------+--------+             +------+--------+
          |      S3       |             |      S2       |
          |  Cisco 2960   |             |  Cisco 2960   |
          +----------------+             +------+---------+
                                                |
                                             Po3 | LACP
                                                |
                                                |
                                          +-----+------+
                                          |     S3     |
                                          +------------+
```

### EtherChannel Links

| Port-Channel | Connection | Protocol |
| ------------ | ---------- | -------- |
| `Po1`        | S1 ↔ S3    | PAgP     |
| `Po2`        | S1 ↔ S2    | LACP     |
| `Po3`        | S2 ↔ S3    | LACP     |

---

## IP Addressing

| Device | Interface | IP Address      | Subnet Mask     |
| ------ | --------- | --------------- | --------------- |
| S1     | VLAN 99   | `192.168.99.11` | `255.255.255.0` |
| S2     | VLAN 99   | `192.168.99.12` | `255.255.255.0` |
| S3     | VLAN 99   | `192.168.99.13` | `255.255.255.0` |
| PC-A   | NIC       | `192.168.10.1`  | `255.255.255.0` |
| PC-B   | NIC       | `192.168.10.2`  | `255.255.255.0` |
| PC-C   | NIC       | `192.168.10.3`  | `255.255.255.0` |

---

## Objectives

### Part 1 — Basic Switch Configuration

Configure the basic parameters of the switches, including:

* Hostnames
* Passwords
* Console and VTY access
* MOTD banner
* Password encryption
* VLAN 10
* VLAN 99
* Access ports
* Management IP addresses
* Configuration saving

### Part 2 — PAgP

Configure an EtherChannel between **S1 and S3** using PAgP.

```text
S1 → desirable
S3 → auto
```

The physical interfaces used are:

```text
S1: Fa0/3 - Fa0/4
S3: Fa0/3 - Fa0/4
```

This creates:

```text
Port-channel 1
```

### Part 3 — LACP

Configure EtherChannels using LACP:

```text
S1 ↔ S2
S2 ↔ S3
```

The configuration uses:

```text
active ↔ passive
```

---

## PAgP Configuration

### S1

```cisco
S1(config)# interface range f0/3-4
S1(config-if-range)# channel-group 1 mode desirable
S1(config-if-range)# no shutdown
```

### S3

```cisco
S3(config)# interface range f0/3-4
S3(config-if-range)# channel-group 1 mode auto
S3(config-if-range)# no shutdown
```

### Configure Port-Channel 1 as a Trunk

#### S1

```cisco
S1(config)# interface port-channel 1
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk native vlan 99
```

#### S3

```cisco
S3(config)# interface port-channel 1
S3(config-if)# switchport mode trunk
S3(config-if)# switchport trunk native vlan 99
```

---

## LACP Configuration

### S1 ↔ S2 — Port-Channel 2

#### S1

```cisco
S1(config)# interface range f0/1-2
S1(config-if-range)# switchport mode trunk
S1(config-if-range)# switchport trunk native vlan 99
S1(config-if-range)# channel-group 2 mode active
S1(config-if-range)# no shutdown
```

#### S2

```cisco
S2(config)# interface range f0/1-2
S2(config-if-range)# switchport mode trunk
S2(config-if-range)# switchport trunk native vlan 99
S2(config-if-range)# channel-group 2 mode passive
S2(config-if-range)# no shutdown
```

---

### S2 ↔ S3 — Port-Channel 3

#### S2

```cisco
S2(config)# interface range f0/3-4
S2(config-if-range)# switchport mode trunk
S2(config-if-range)# switchport trunk native vlan 99
S2(config-if-range)# channel-group 3 mode active
S2(config-if-range)# no shutdown
```

#### S3

```cisco
S3(config)# interface range f0/1-2
S3(config-if-range)# switchport mode trunk
S3(config-if-range)# switchport trunk native vlan 99
S3(config-if-range)# channel-group 3 mode passive
S3(config-if-range)# no shutdown
```

---

## Verification

### EtherChannel Summary

```cisco
show etherchannel summary
```

Expected PAgP output:

```text
Group  Port-channel  Protocol  Ports
------+-------------+---------+---------------------------
1      Po1(SU)      PAgP      Fa0/3(P) Fa0/4(P)
```

The `P` flag indicates that the physical interface is bundled into the port-channel. `S` indicates a Layer 2 EtherChannel, while `U` indicates that the port-channel is in use.

### Additional Verification Commands

```cisco
show running-config interface f0/3
show interfaces f0/3 switchport
show interfaces trunk
show spanning-tree
show interfaces status
```

---

## Connectivity Testing

After completing the configuration, verify end-to-end connectivity with ICMP:

```text
PC-A → PC-B
PC-A → PC-C
PC-B → PC-C
```

The lab requires the devices to successfully communicate within the same VLAN.

---

## PAgP vs LACP

| Feature        | PAgP               | LACP               |
| -------------- | ------------------ | ------------------ |
| Standard       | Cisco proprietary  | IEEE 802.3ad       |
| Negotiation    | `desirable / auto` | `active / passive` |
| Port-Channel   | `Po1`              | `Po2`, `Po3`       |
| Lab Connection | S1 ↔ S3            | S1 ↔ S2 / S2 ↔ S3  |

PAgP is Cisco proprietary, whereas LACP is an IEEE-defined link aggregation protocol.

---

## EtherChannel Summary

| Port-Channel | Devices | Interfaces        | Protocol | Mode             |
| ------------ | ------- | ----------------- | -------- | ---------------- |
| `Po1`        | S1 ↔ S3 | Fa0/3-4           | PAgP     | desirable / auto |
| `Po2`        | S1 ↔ S2 | Fa0/1-2           | LACP     | active / passive |
| `Po3`        | S2 ↔ S3 | Fa0/3-4 ↔ Fa0/1-2 | LACP     | active / passive |

---

## Key Concepts

This lab demonstrates:

* EtherChannel
* Link aggregation
* PAgP
* LACP
* Port-channel interfaces
* VLAN configuration
* 802.1Q trunking
* Native VLANs
* Layer 2 EtherChannel
* EtherChannel verification
* Network troubleshooting

---

## Repository Structure

```text
configuring-etherchannel/
│
├── README.md
│
├── configs/
│   ├── S1.txt
│   ├── S2.txt
│   └── S3.txt
│
├── screenshots/
│   ├── topology.png
│   ├── etherchannel-summary.png
│   ├── trunk-status.png
│   └── connectivity-test.png
│
└── lab/
    └── 3.2.1.4-Lab-Configuring-EtherChannel.pdf
```

---

## Reference

**Cisco Networking Academy — 3.2.1.4 Lab: Configuring EtherChannel**

This repository is based on the provided Cisco lab material covering basic switch configuration, PAgP, LACP, trunking, EtherChannel verification, and end-to-end connectivity.

---

<p align="center">
  <sub>Cisco Networking Lab • EtherChannel • PAgP • LACP • CCNA</sub>
</p>
