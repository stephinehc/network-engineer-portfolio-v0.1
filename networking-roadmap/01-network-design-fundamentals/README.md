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
- **Requirement**
  - Background about converting Decimal Number to Binary Number and vice versa.

### IPv4 Address Stucture and Subnet Mask
An IPv4 address is composed of 32 bits and is divided into four octets. It is represented in decimal number system format. <br> <br>
A subnet mask determines which octets belong to the network portion and host portion. <br>
Example:<br>
192.168.30.1/24<br>
The prefix /24 represents the number of network bits (starting from the 1st octet going to the right). The
/24 means 1111 1111.1111 1111.1111 1111.0000 0000 in binary. If converted in decimal, 255.255.255.0 which is called the subnet mask. Those binary `1` are the network bits while the binary `0` are the host bits.
<br> <br>
To determine the network IP address, we use the boolean logic AND. We call it the AND-ing process. AND-ing the binary values of the IP adress and subnet mask.

### IPv4 Address Classes

| Class | Starting Address | Ending Address | Default Subnet Mask | Notes |
|-------|------------------|----------------|---------------------|-------|
| A     | 0.0.0.0          | 127.255.255.255| 255.0.0.0 or /8           | Designed for very large networks |
| B     | 128.0.0.0        | 191.255.255.255| 255.255.0.0 or /16         | Medium-sized networks |
| C     | 192.0.0.0        | 223.255.255.255| 255.255.255.0 or /24       | Small networks |
| D     | 224.0.0.0        | 239.255.255.255| N/A                 | Reserved for multicast |
| E     | 240.0.0.0        | 255.255.255.255| N/A                 | Reserved for experimental use |

  ### Subnetting
  - Subdividing a single network into multiple networks by borrowing some bits from the host portion and making it as a part of the network portion. <br><br>
  Two ways in performing Subnetting
    - **Case A**: If requirement is number of Networks (Movement of bits is from Left to Right)
    - **Case B**: If requirement is number of Hosts/Subnet (Movement of bits is from Right to Left)
   
### CIDR (Classless Inter-Domain Routing)
  - A method that no longer follow the class-based addressing
  - Example: 10.0.0.0/16 (notice that the IPv4 address 10.0.0.0 belongs to class A but the prefix (/16) belongs to class B)
<br>

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
- Number of subnets (x_s) = \(2^n\) (where *n* = network bits borrowed from host bits)
  - $x_s \geq \text{required networks}$
- Usable Hosts per subnet (x_h) = \(2^h - 2\) (where *h* = remaining host bits) <br> NOTE: the "-2" refers to the Network IP and Broadcast IP per subnet
  - $x_h \geq \text{required hosts/network}$

### 4. Customized Subnet Mask
- Borrow bits from the host portion to create subnets.
- Example: `255.255.255.192` (borrowed 2 bits from Class C)
  ### Subnet Mask Reference

  | No. of Network Bits in an Octet | Equivalent Decimal Value |
  |----------------------|--------------------------|
  | 1                    | 128                      |
  | 2                    | 192                      |
  | 3                    | 224                      |
  | 4                    | 240                      |
  | 5                    | 248                      |
  | 6                    | 252                      |
  | 7                    | 254                      |
  | 8                    | 255                      |

### 5. IP Range
- Calculate the valid host range for each subnet.
- Example: Subnet `192.168.1.0/26` → Range `192.168.1.1 – 192.168.1.62`

### 6. Subnet Table
- Summarize all subnets for clarity and quick reference.

---

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

---

### Steps

### 1. Identify the Default Class of the Network IP
- Given IP: Class A 

### 2. Identify the Default Subnet Mask
- Class A → `/8` → `255.0.0.0`

### 3. Network ID
- Representation: `N.H.H.H`  
- Binary mask: ![Red](https://img.shields.io/badge/1111%201111-red)
![Blue](https://img.shields.io/badge/.0000%200000.0000%200000.0000%200000-blue)
- Host bits available: 24  
- Requirement: At least 2 subnets (Production and Administration)  
- Formula: \(x_s = 2^n\)  
  - Borrowed bits: \(n = 1\) (Borrow 1 bit from the leftmost host portion)
  - Subnets: \(2^1 = 2\)  
- Remaining host bits: 23  
- Hosts per subnet: \(x_h = (2^{23}) - 2 = 8,388,606\)
- New Binary Mask: ![Red](https://img.shields.io/badge/1111%201111.1-red)
![Blue](https://img.shields.io/badge/000%200000.0000%200000.0000%200000-blue)

### 4. Customized Subnet Mask
- New mask: `/8 + 1 = /9`  
- Equivalent: `255.128.0.0`

### 5. IP Range
- Block size: \(256 - 128 = 128\)
- Add the value `128` to the recent modified octet. In our case, the recent modified octet is the 2nd octet.

### 6. Subnet Table
- The 1st subnet is the provided network IP address then we add the block size to the 2nd octet.
- When working with the subnet table, do TOP to bottom approach starting from the network IP address then broadcast IP address and then lastly, the Range of usable host IPs.

| Subnet No. | Network IP Address | Range of Usable Host IPs       | Broadcast IP Address |
|------------|--------------------|--------------------------------|----------------------|
| 1          | 10.**0**.0.0         | 10.0.0.1 – 10.127.255.254      | 10.**127**.255.255     |
| 2          | 10.**128**.0.0       | 10.128.0.1 – 10.255.255.254    | 10.**255**.255.255     |

### Assigning the network IP
<img width="649" height="346" alt="image" src="https://github.com/user-attachments/assets/d3e611b3-5072-499e-96ac-19552b4e03a7" />

<p align="justify">
If we use Class A, there are approximately 8.4 million host IP addresses that are available in each subnet in which, there are a lot of wasted IP addresses since the network topology is just for a small company. In addition, the network is not scalable since there are no reserve network IP addresses in case if the company will expand. Although, we can also consider using another network IP address if the company is planning to expand but it is an additional task. 
</p>  
<p align="justify">
Always consider the best network design and not just use a random network IP class. Here are the things that needs to be considered in network design: Scalability, Reliability, Fault Tolerance, Availability, QoS, and Security.
</p>
 
<p align="justify">
Base on the network design we have, I can say that it is poorly designed. So, let us go ahead and correct it. Take note that our goal for now is to have a scalable network. Proceed to Class B subnetting.
</p>

[Class B Subnetting](../01-network-design-fundamentals/class-b/README.md)

---

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br> [Next →](../02-lan-technologies/README.md)
