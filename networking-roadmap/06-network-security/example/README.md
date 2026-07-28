[Basic Network Security](../README.md)

---

# Network Security & Cisco Access Control List (ACL) Implementation Guide
> The network design provided is the continuation of the scenario for **7. High Availability (HSRP)**, so I recommend first to proceed to the topic [7. High Availability (HSRP)](./networking-roadmap/07-high-availability/README.md) then go back to this scenario.

## Executive Summary

This document provides a comprehensive, production-ready Cisco Access Control List (ACL) configuration tailored specifically to the provided network topology. The implementation leverages both **Standard** and **Extended ACLs** to achieve defense-in-depth, strict departmental isolation, administrative service restriction, and perimeter edge protection.

We will secure that each department will be isolated from each other and implement NAT(PAT) in order for the WAN not be able to directly communicate our network.

Expected results:
```text
Ping its own gateway                 Should succeed
Ping another user VLAN               Should fail
SSH to router or switch              Should fail
Query 142.250.204.2 for DNS          Should succeed
Browse an Internet TCP service       Should succeed
Ping 142.250.204.2                   Should succeed if echo replies are allowed
```

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
  permit tcp host 142.250.204.2 eq domain any established
  permit icmp host 142.250.204.2 any echo-reply
  permit icmp any any unreachable

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
  permit tcp 192.168.10.0 0.0.0.255 host 142.250.204.2 eq domain
  permit icmp any any echo-reply
  permit icmp any any unreachable

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
  permit tcp 192.168.20.0 0.0.0.255 host 142.250.204.2 eq domain
  permit icmp any any echo-reply
  permit icmp any any unreachable

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
  permit tcp 192.168.30.0 0.0.0.255 host 142.250.204.2 eq domain
  permit icmp any any echo-reply
  permit icmp any any unreachable

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
### 4. Verification

All VLANs should now be isolated and cannot ping each other (refer to the PDU list window).

<img width="1920" height="1003" alt="image" src="https://github.com/user-attachments/assets/f2c4a1fe-e3c3-4b27-9fc1-f204ed7ef85c" />



All VLANs should be able to continuously ping the DNS server.
<img width="1920" height="1017" alt="image" src="https://github.com/user-attachments/assets/fe37b936-5a74-4a07-92fb-7bbcda17cba0" />



Let us verify if the HSRP will work after we simulate the link failure **ARtr**. See the lower left corner, the **SRtr** now becomes the active router and at the same time some packets were dropped while the PCs are pinging the DNS server which is natural.

<img width="1920" height="1006" alt="image" src="https://github.com/user-attachments/assets/7254074d-1f97-4f17-a3fa-2538c0bcc40f" />

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

# NAT Implementation
On both routers (ARtr and SRtr), we will create a standard ACL.
```text
ip access-list standard VLAN10
 permit 192.168.10.0 0.0.0.255
ip access-list standard VLAN20
 permit 192.168.20.0 0.0.0.255
ip access-list standard VLAN30
 permit 192.168.30.0 0.0.0.255

ip nat inside source list VLAN10 interface Serial0/0/0 overload
ip nat inside source list VLAN20 interface Serial0/0/0 overload
ip nat inside source list VLAN30 interface Serial0/0/0 overload

int s0/0/0
 ip nat outside

int g0/0.10
 ip nat inside
int g0/0.20
 ip nat inside
int g0/0.30
 ip nat inside
```

To verify the NAT:
```text
show ip nat translations
```

The expected result would be only the ARtr should perform the NAT since it is the active router. As shown in the figure in the lower right corner, there is no translation happened at the SRtr.
<img width="1920" height="995" alt="image" src="https://github.com/user-attachments/assets/19e8bc9a-6de1-4469-8d45-8eea5ada06a6" />



To verify that SRtr will perform NAT, let us simulate a link failure of ARtr.
<img width="1920" height="1018" alt="image" src="https://github.com/user-attachments/assets/46cbd0ee-ee5c-4056-899d-6a0006d8cc93" />

---

[Basic Network Security](../README.md)
