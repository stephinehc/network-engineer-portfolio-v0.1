# 6. Basic Network Security (ACLs, NAT)

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br> [← Previous](../05-network-services/README.md) · [Next →](../07-high-availability/README.md)

---


## Overview
<p align="justify">
This section introduces the fundamental security mechanisms used in enterprise networks. Access Control Lists (ACLs) provide traffic filtering and policy enforcement, while Network Address Translation (NAT) enables private IP addressing schemes to communicate with external networks securely. Together, ACLs and NAT form the backbone of perimeter defense and traffic management.
</p>

---

## Topics Covered
- Access Control Lists (ACLs)
  - Standard vs Extended ACLs
  - Numbered vs Named ACLs
  - Wildcard masks
  - Placement rules (inbound vs outbound)
  - Common use cases (restricting access, filtering traffic)
- Network Address Translation (NAT)
  - Static NAT
  - Dynamic NAT
  - Port Address Translation (PAT)
  - NAT configuration and troubleshooting
- Security considerations and best practices

---

# Network Security Fundamentals

Network security helps protect devices and data by controlling network access and securing communication. Two commonly used security technologies in enterprise networks are **Access Control Lists (ACLs)** and **Network Address Translation (NAT)**.

---

# Access Control Lists (ACLs)

An **Access Control List (ACL)** is a set of rules used by routers and Layer 3 switches to **permit or deny network traffic** based on defined criteria.

ACLs are commonly used to:

- Restrict access to networks or devices
- Filter unwanted traffic
- Improve network security
- Control traffic flow between networks

---

## Standard vs Extended ACLs

### Standard ACL

- Filters traffic based only on the **source IP address**.
- Simpler to configure.
- Typically placed **close to the destination**.

Range:

```text
1–99
1300–1999
```

---

### Extended ACL

- Filters traffic based on:
  - Source IP address
  - Destination IP address
  - Protocol (TCP, UDP, ICMP)
  - Port numbers

- Provides more granular control.
- Typically placed **close to the source**.

Range:

```text
100–199
2000–2699
```

---

## Numbered vs Named ACLs

### Numbered ACL

Uses a numeric identifier.

Example:

```bash
access-list 10 permit 192.168.10.0 0.0.0.255
```

### Named ACL

Uses a descriptive name, making configurations easier to read and maintain.

Example:

```bash
ip access-list standard STAFF
 permit 192.168.10.0 0.0.0.255
```

---

## Wildcard Masks

ACLs use **wildcard masks** instead of subnet masks.

A wildcard mask is the inverse of a subnet mask.

Examples:

| Subnet Mask | Wildcard Mask |
|--------------|---------------|
| 255.255.255.0 | 0.0.0.255 |
| 255.255.255.252 | 0.0.0.3 |
| 255.255.0.0 | 0.0.255.255 |

---

## Placement Rules

ACLs can be applied:

- **Inbound** – Filters packets as they enter an interface.
- **Outbound** – Filters packets before leaving an interface.

General guidelines:

- **Standard ACLs:** Place **close to the destination**.
- **Extended ACLs:** Place **close to the source**.

---

## Common Use Cases

ACLs are commonly used to:

- Restrict access to specific networks.
- Block unwanted traffic.
- Permit or deny specific protocols or ports.
- Control remote management access (SSH, Telnet).
- Improve network security.

---

# Network Address Translation (NAT)

**Network Address Translation (NAT)** translates private IP addresses into public IP addresses, allowing multiple internal devices to communicate with external networks such as the Internet.

Benefits include:

- Conserves public IPv4 addresses.
- Hides internal IP addresses.
- Improves network security.

---

## Static NAT

Provides a **one-to-one** mapping between a private IP address and a public IP address.

Commonly used for:

- Web servers
- Mail servers
- Devices that must always be reachable from the Internet

---

## Dynamic NAT

Maps private IP addresses to a **pool of public IP addresses**.

A public IP is assigned only while the device is communicating.

---

## Port Address Translation (PAT)

Also called **NAT Overload**.

Allows **multiple private IP addresses** to share **a single public IP address** by using different TCP or UDP port numbers.

PAT is the most commonly used NAT method in homes and businesses.

---

## Basic NAT Configuration

Example PAT configuration:

```bash
access-list 1 permit 192.168.10.0 0.0.0.255

ip nat inside source list 1 interface GigabitEthernet0/0 overload

interface GigabitEthernet0/0
 ip nat outside

interface GigabitEthernet0/1
 ip nat inside
```

Verification commands:

```bash
show ip nat translations
show ip nat statistics
```

---

## NAT Troubleshooting

Common issues include:

- Incorrect inside or outside interface configuration.
- Missing ACL for NAT.
- Incorrect public interface.
- Missing default route.
- ACL blocking translated traffic.

Useful commands:

```bash
show ip nat translations
show ip nat statistics
show running-config
show ip interface brief
```

---

# Security Considerations and Best Practices

- Apply the **principle of least privilege** by allowing only necessary traffic.
- Use **Extended ACLs** when filtering by protocol or port.
- Place ACLs according to Cisco's recommended placement rules.
- Regularly review and update ACL entries.
- Use **PAT** to conserve public IPv4 addresses.
- Combine NAT with firewalls and other security mechanisms for better protection.
- Verify configurations after implementation using troubleshooting commands.

---

# Summary

- **ACLs** filter network traffic by permitting or denying packets based on defined rules.
- **Standard ACLs** filter only the source IP address, while **Extended ACLs** can also filter destination addresses, protocols, and ports.
- **NAT** translates private IP addresses to public IP addresses, enabling Internet connectivity and conserving IPv4 addresses.
- **Static NAT** provides one-to-one translation, **Dynamic NAT** uses a pool of public addresses, and **PAT** allows many devices to share a single public IP address.
- Proper ACL placement, NAT configuration, and regular verification are essential for maintaining a secure and reliable network.

# Network Design Project
> The network design provided is the continuation of the scenario for **7. High Availability (HSRP)**, so I recommend first to proceed to the topic [7. High Availability (HSRP)](../07-high-availability/README.md) then go back to this scenario.

[ABC Technologies – Enterprise Network Design with ACL implementation](../06-network-security/example/README.md)

---


[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br> [← Previous](../05-network-services/README.md) · [Next →](../07-high-availability/README.md)
