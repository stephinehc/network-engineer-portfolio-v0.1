# 7. High Availability (HSRP)

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br> [← Previous](../06-network-security/README.md) · [Next →](../08-secure-wan/README.md)

---

## Overview
<p align="justify">
This section introduces the concept of high availability in enterprise networks, focusing on the Hot Standby Router Protocol (HSRP). HSRP ensures network resilience by providing a virtual default gateway that remains available even if one router fails. This redundancy minimizes downtime and guarantees continuous connectivity for end users.
</p>

---

## Topics Covered
- High availability principles in networking
- Hot Standby Router Protocol (HSRP)
  - Purpose and operation
  - Active and standby router roles
  - Virtual IP and MAC addresses
- HSRP states (Initial, Learn, Listen, Speak, Standby, Active)
- Priority and preemption configuration
- Load balancing with multiple HSRP groups
- Troubleshooting HSRP issues

---

# High Availability (HA) Principles in Networking

**High Availability (HA)** ensures that network services remain operational even if a device or link fails. HA is achieved through **redundancy**, **failover mechanisms**, and **fault tolerance**, minimizing network downtime.

Common HA technologies include **HSRP**, **VRRP**, and **GLBP**.

---

# Hot Standby Router Protocol (HSRP)

**HSRP (Hot Standby Router Protocol)** is a Cisco proprietary first-hop redundancy protocol that provides **gateway redundancy**. If the active router fails, the standby router automatically takes over as the default gateway.

---

## Purpose and Operation

HSRP allows multiple routers to appear as a **single default gateway** by sharing:

- Virtual IP Address
- Virtual MAC Address

Only one router actively forwards traffic while another remains on standby.

---

## Active and Standby Router Roles

- **Active Router** – Forwards traffic for the virtual gateway.
- **Standby Router** – Monitors the active router and takes over if it fails.
- **Other Routers** – Remain in the Listen state (if present).

---

## Virtual IP and MAC Addresses

- **Virtual IP Address** – Used as the hosts' default gateway.
- **Virtual MAC Address** – Shared by the HSRP group.

Example:

```text
PC Default Gateway

192.168.10.254 (Virtual IP)

      |
      |
+-------------+
| HSRP Group  |
+-------------+
   |       |
 Active   Standby
```

---

# HSRP States

HSRP routers transition through the following states:

| State | Description |
|--------|-------------|
| Initial | HSRP starts. |
| Learn | Learns the virtual IP address. |
| Listen | Receives HSRP messages. |
| Speak | Participates in HSRP elections. |
| Standby | Backup router waiting to take over. |
| Active | Currently forwarding traffic. |

---

# Priority and Preemption

HSRP selects the **Active Router** based on **priority**.

- Default Priority: **100**
- Higher priority becomes the Active Router.

Example:

```bash
interface g0/0
 standby 1 ip 192.168.10.254
 standby 1 priority 120
 standby 1 preempt
```

- **Preemption** allows a higher-priority router to reclaim the Active role after recovering from a failure.

---

# Load Balancing with Multiple HSRP Groups

HSRP does not load balance within a single group. However, **multiple HSRP groups** can distribute traffic by assigning different VLANs or subnets to different Active Routers.

Example:

| HSRP Group | Active Router |
|------------|---------------|
| Group 1 | R1 |
| Group 2 | R2 |

This allows both routers to actively forward traffic for different groups.

---

# Troubleshooting HSRP Issues

Common verification commands:

```bash
show standby
show standby brief
show running-config
show ip interface brief
```

Common issues:

- Priority configured incorrectly.
- Preemption not enabled.
- HSRP group number mismatch.
- Virtual IP address mismatch.
- Interface is down.
- Authentication mismatch (if configured).

---

# Summary

- **High Availability (HA)** minimizes downtime through redundancy.
- **HSRP** provides default gateway redundancy using a virtual IP and virtual MAC address.
- One router is **Active**, while another is **Standby**.
- HSRP uses six states: **Initial, Learn, Listen, Speak, Standby, and Active**.
- **Priority** determines the Active Router, while **preemption** allows a higher-priority router to regain control.
- Multiple HSRP groups can provide basic load balancing across routers.

# Network Design Project
[ABC Technologies – Enterprise Network Design](../07-high-availability/example/README.md)
---

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br> [← Previous](../06-network-security/README.md) · [Next →](../08-secure-wan/README.md)
