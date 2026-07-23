[Basic Network Security](../README.md)

---

# Network Security & Cisco Access Control List (ACL) Implementation Guide
> The network design provided is the continuation of the scenario for **7. High Availability (HSRP)**, so I recommend first to proceed to the topic [7. High Availability (HSRP)](../07-high-availability/README.md) then go back to this scenario.

## Executive Summary

This document provides a comprehensive, production-ready Cisco Access Control List (ACL) configuration tailored specifically to the provided network topology. The implementation leverages both **Standard** and **Extended ACLs** to achieve defense-in-depth, strict departmental isolation, administrative service restriction, and perimeter edge protection.

---
<img width="1419" height="647" alt="image" src="https://github.com/user-attachments/assets/346257a0-af0d-4820-99b0-12dcffb7432b" />

## Network Overview & IP Addressing Scheme

| Entity / Segment | Description / Function | IP Subnet / Address | Default Gateway / VRIP |
| :--- | :--- | :--- | :--- |
| **VLAN 10** | Sales Department | `192.168.10.0/24` | `192.168.10.11` |
| **VLAN 20** | Billing Department | `192.168.20.0/24` | `192.168.20.12` |
| **VLAN 30** | HR Department | `192.168.30.0/24` | `192.168.30.13` |
| **VLAN 99** | Security / Isolated | N/A | N/A |
| **VLAN 100** | Network Management | `192.168.100.0/24` | `192.168.100.10` |
| **ARtr (Active)** | Active Gateway Router | `192.168.x.1` / `200.0.2.1/30` | N/A |
| **SRtr (Standby)** | Standby Gateway Router | `192.168.x.2` / `200.0.1.1/30` | N/A |
| **WAN / DNS** | External Public DNS Server | `142.250.204.2` | N/A |

---

## Security Matrix & Enforcement Strategy

| Source Segment | Destination / Target | Permitted Services | Action | Security Rationale |
| :--- | :--- | :--- | :--- | :--- |
| **VLAN 10 (Sales)** | External DNS (`142.250.204.2`) | UDP 53 (DNS) | **PERMIT** | Necessary for domain name resolution |
| **VLAN 10 (Sales)** | VLAN 20, 30, 100 | Any | **DENY** | Enforce departmental data isolation |
| **VLAN 10 (Sales)** | Network Infrastructure | TCP 22, 23 / UDP 161, 162 | **DENY** | Prevent SSH/Telnet/SNMP management access |
| **VLAN 20 (Billing)**| External DNS (`142.250.204.2`) | UDP 53 (DNS) | **PERMIT** | Necessary for domain name resolution |
| **VLAN 20 (Billing)**| VLAN 10, 30, 100 | Any | **DENY** | Protect financial and billing records |
| **VLAN 20 (Billing)**| Network Infrastructure | TCP 22, 23 / UDP 161, 162 | **DENY** | Prevent unauthorized router/switch access |
| **VLAN 30 (HR)** | External DNS (`142.250.204.2`) | UDP 53 (DNS) | **PERMIT** | Necessary for domain name resolution |
| **VLAN 30 (HR)** | VLAN 10, 20, 100 | Any | **DENY** | Safeguard confidential employee records |
| **VLAN 30 (HR)** | Network Infrastructure | TCP 22, 23 / UDP 161, 162 | **DENY** | Prevent administrative access attempts |
| **VLAN 100 (Mgmt)**| All Network Devices | SSH, Telnet, SNMP | **PERMIT** | Authorized network administration subnet |
| **WAN Edge** | Internal Subnets | Response Traffic Only | **FILTER** | RFC 1918 anti-spoofing & unauthorized access block |

---

## Comprehensive Cisco ACL Configuration

### 1. Perimeter & Internet Edge Filtering

*Applied inbound on the WAN Serial interface (`s0/0/0`) on **ARtr** to block spoofed packets, external management attempts, and unrequested inbound WAN traffic.*

```text
! Enable Extended ACL for Perimeter Defense
ip access-list extended WAN_EDGE_IN_ACL

  ! Permit DNS response traffic from the specific external DNS server
  permit udp host 142.250.204.2 eq domain any

  ! Anti-Spoofing Rules: Block private IP addresses originating from the public WAN (RFC 1918)
  deny ip 10.0.0.0 0.255.255.255 any
  deny ip 172.16.0.0 0.15.255.255 any
  deny ip 192.168.0.0 0.255.255.255 any

  ! Explicitly block management protocols originating from the Internet
  deny tcp any any eq 22        ! SSH (Secure Shell)
  deny tcp any any eq 23        ! Telnet
  deny udp any any eq 161       ! SNMP (Simple Network Management Protocol)
  deny udp any any eq 162       ! SNMP Traps

  ! Implicit Deny All drops all other unsolicited external traffic

! Apply to external WAN Interface
interface s0/0/0
  ip access-group WAN_EDGE_IN_ACL in
```

---

### 2. Administrative Access Restriction (SSH, Telnet, SNMP)

*Uses a **Standard ACL** to strictly limit administrative management (VTY lines and SNMP community strings) exclusively to **VLAN 100 (Management)**.*

#### A. Standard ACL Definition
```text
! Standard ACL filtering solely based on Source IP Address
ip access-list standard ADMIN_ACCESS_ONLY
  ! Permit authorized management subnet (VLAN 100)
  permit 192.168.100.0 0.0.0.255
  ! Implicit Deny All automatically blocks VLAN 10, VLAN 20, VLAN 30, and external networks
```

#### B. VTY Line Hardening (Routers & Switches: ARtr, SRtr, S1, S2)
```text
! Restrict terminal access to SSH and enforce source address check
line vty 0 15
  transport input ssh
  access-class ADMIN_ACCESS_ONLY in
```

#### C. SNMP Community String Restriction
```text
! Restrict SNMP Read-Only and Read-Write access to Management VLAN
snmp-server community ADMIN_RO ro ADMIN_ACCESS_ONLY
snmp-server community ADMIN_RW rw ADMIN_ACCESS_ONLY
```

---

### 3. Inter-VLAN Sub-Interface ACLs (User Subnets)

*Applied inbound on router sub-interfaces (`g0/0.10`, `g0/0.20`, `g0/0.30`) on both **ARtr** and **SRtr** (for HSRP failover alignment).*

#### A. VLAN 10 (Sales Department)
```text
ip access-list extended SALES_IN_ACL
  ! Allow outbound DNS requests to external DNS server
  permit udp 192.168.10.0 0.0.0.255 host 142.250.204.2 eq domain

  ! Explicitly block management protocols targeting any destination
  deny tcp 192.168.10.0 0.0.0.255 any eq 22
  deny tcp 192.168.10.0 0.0.0.255 any eq 23
  deny udp 192.168.10.0 0.0.0.255 any eq 161
  deny udp 192.168.10.0 0.0.0.255 any eq 162

  ! Deny inter-VLAN access to Billing, HR, and Management subnets
  deny ip 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255
  deny ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255
  deny ip 192.168.10.0 0.0.0.255 192.168.100.0 0.0.0.255

  ! Permit all other legitimate outbound/Internet traffic
  permit ip 192.168.10.0 0.0.0.255 any

interface g0/0.10
  ip access-group SALES_IN_ACL in
```

#### B. VLAN 20 (Billing Department)
```text
ip access-list extended BILLING_IN_ACL
  ! Allow outbound DNS requests to external DNS server
  permit udp 192.168.20.0 0.0.0.255 host 142.250.204.2 eq domain

  ! Explicitly block management protocols
  deny tcp 192.168.20.0 0.0.0.255 any eq 22
  deny tcp 192.168.20.0 0.0.0.255 any eq 23
  deny udp 192.168.20.0 0.0.0.255 any eq 161
  deny udp 192.168.20.0 0.0.0.255 any eq 162

  ! Deny inter-VLAN access to Sales, HR, and Management subnets
  deny ip 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255
  deny ip 192.168.20.0 0.0.0.255 192.168.30.0 0.0.0.255
  deny ip 192.168.20.0 0.0.0.255 192.168.100.0 0.0.0.255

  ! Permit all other legitimate outbound traffic
  permit ip 192.168.20.0 0.0.0.255 any

interface g0/0.20
  ip access-group BILLING_IN_ACL in
```

#### C. VLAN 30 (HR Department)
```text
ip access-list extended HR_IN_ACL
  ! Allow outbound DNS requests to external DNS server
  permit udp 192.168.30.0 0.0.0.255 host 142.250.204.2 eq domain

  ! Explicitly block management protocols
  deny tcp 192.168.30.0 0.0.0.255 any eq 22
  deny tcp 192.168.30.0 0.0.0.255 any eq 23
  deny udp 192.168.30.0 0.0.0.255 any eq 161
  deny udp 192.168.30.0 0.0.0.255 any eq 162

  ! Deny inter-VLAN access to Sales, Billing, and Management subnets
  deny ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255
  deny ip 192.168.30.0 0.0.0.255 192.168.20.0 0.0.0.255
  deny ip 192.168.30.0 0.0.0.255 192.168.100.0 0.0.0.255

  ! Permit all other legitimate outbound traffic
  permit ip 192.168.30.0 0.0.0.255 any

interface g0/0.30
  ip access-group HR_IN_ACL in
```

---

## Switch Hardening & Complementary Security

To support Layer 3 ACLs, implement these essential Layer 2 switchport security configurations on **S1** and **S2**:

```text
! Lock down end-user access ports on S1 and S2
interface range FastEthernet 0/1 - 3
  switchport mode access
  switchport port-security
  switchport port-security maximum 1
  switchport port-security violation restrict
  switchport port-security mac-address sticky

! Isolate all unused switch ports into VLAN 99 and disable them
interface range FastEthernet 0/4 - 22
  switchport access vlan 99
  shutdown
```

---

[Basic Network Security](../README.md)
