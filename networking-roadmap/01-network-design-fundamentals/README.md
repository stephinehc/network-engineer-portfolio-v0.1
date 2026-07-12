# 1. Network Design Fundamentals (Subnetting & VLSM)

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br> <br>[Next →](../02-lan-technologies/README.md)

---


## Overview
<p align="justify">
This section introduces the foundational concepts of IP addressing, subnetting, and Variable Length Subnet Masking (VLSM). These skills are essential for designing efficient, scalable, and organized networks. Mastery of subnetting ensures proper allocation of IP addresses, while VLSM allows flexible design tailored to specific network requirements.
</p>

---

## Topics Covered
- IPv4 address structure (Network ID, Host ID)
- Subnet masks and CIDR notation
- Subnetting step-by-step process
- Calculating usable host ranges
- Variable Length Subnet Masking (VLSM) for hierarchical design
- Address planning for LANs and WANs
- Common subnetting scenarios in enterprise networks

---

## Notes & Diagrams
- **Subnetting**
  - **Case A**: If requirement is number of Networks (Subnets)
  - **Case B**: If requirement is number of Hosts/Subnet

## 5 Easy Steps in Doing Subnetting
### 1. Identify the Default Class of the Network IP
- Determine whether the IP address belongs to Class A, B, or C.
- Example: `192.168.1.0` → Class C

### 2. Identify the Default Subnet Mask
- Each class has a default mask:
  - Class A → `255.0.0.0` or `/8`
  - Class B → `255.255.0.0` or `/16`
  - Class C → `255.255.255.0` or `/24`

### 3. Network ID
- The network ID is the portion of the IP defined by the default mask.
- Example: `192.168.1.0` with mask `255.255.255.0` → Network ID is `N.N.N.H` where `N` represents the network portion and `H` as host portion
- Number of subnets (x_s) = \(2^n\) (where *n* = borrowed bits host bits)
- Usable Hosts per subnet (x_h) = \(2^h - 2\) (where *h* = remaining host bits) <br> NOTE: the "-2" refers to the Network IP and Broadcast IP per subnet

### 4. Customized Subnet Mask
- Borrow bits from the host portion to create subnets.
- Example: `255.255.255.192` (borrowed 2 bits from Class C)

### 5. IP Range
- Calculate the valid host range for each subnet.
- Example: Subnet `192.168.1.0/26` → Range `192.168.1.1 – 192.168.1.62`

### 6. Subnet Table
- Summarize all subnets for clarity and quick reference.


### Case A Scenario <br>
**Company**: EduTech Manufacturing Corp.  <br>
**Industry**: Educational equipment and technology solutions<br>
They have two major departments that need separate networks:

**Production Department**<br>
Handles manufacturing of school supplies and tech devices.<br>
Requires a large subnet for factory machines, IoT sensors, and production servers.

**Administration Department**<br>
Includes HR, Finance, and Management offices.<br>
Needs a smaller subnet for office PCs, printers, and internal applications.

  <img width="660" height="339" alt="image" src="https://github.com/user-attachments/assets/e2646d27-a480-4470-9e83-579327cdd15a" />


### Steps

### 1. Identify the Default Class of the Network IP
- Given IP: Class A  
- Default subnet mask (SM): `255.0.0.0` or `/8`

### 2. Identify the Default Subnet Mask
- Class A → `/8` → `255.0.0.0`

### 3. Network ID
- Representation: `N.H.H.H`  
- Binary mask: `11111111.00000000.00000000.00000000`  
- Host bits available: 24  
- Requirement: At least 2 subnets (Production and Administration)  
- Formula: \(x_s = 2^n\)  
  - Borrowed bits: \(n = 1\)  
  - Subnets: \(2^1 = 2\)  
- Remaining host bits: 23  
- Hosts per subnet: \(x_h = (2^{23}) - 2 = 8,388,606\)

### 4. Customized Subnet Mask
- New mask: `/8 + 1 = /9`  
- Equivalent: `255.128.0.0`

### 5. IP Range
- Block size: \(256 - 128 = 128\)

### 6. Subnet Table

| Subnet No. | Network IP Address | Range of Usable Host IPs       | Broadcast IP Address |
|------------|--------------------|--------------------------------|----------------------|
| 1          | 10.**0**.0.0/9         | 10.0.0.1 – 10.127.255.254      | 10.**127**.255.255/9     |
| 2          | 10.**128**.0.0/9       | 10.128.0.1 – 10.255.255.254    | 10.**255**.255.255/9     |

### Assigning the network IP
<img width="649" height="346" alt="image" src="https://github.com/user-attachments/assets/d3e611b3-5072-499e-96ac-19552b4e03a7" />

---

## Resources
- 📖 [Cisco Networking Academy: Subnetting](https://www.netacad.com/courses/networking)
- 📘 [RFC 950: Internet Standard Subnetting Procedure](https://www.rfc-editor.org/rfc/rfc950)
- 🧮 Online Subnet Calculator: [Subnetting Practice Tool](https://www.subnetting.net)
- 🎥 YouTube: “Subnetting Made Easy” tutorials

---

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br> [Next →](../02-lan-technologies/README.md)
