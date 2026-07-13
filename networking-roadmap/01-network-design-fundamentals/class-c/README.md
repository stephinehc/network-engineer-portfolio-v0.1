[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br> [Next →](../02-lan-technologies/README.md)

### Class C Subnetting <br>
**Company**: EduTech Manufacturing Corp.  <br>
**Industry**: Educational equipment and technology solutions<br>
They have expanded and needs separate networks:

**Production Department**<br>
It has now expanded to Prod1, Prod2, and Prod3. <br>
Handles manufacturing of school supplies and tech devices.<br>
Requires a large subnet for factory machines, IoT sensors, and production servers.

**Administration Department**<br>
Includes HR, Finance, and Management offices.<br>
Needs a smaller subnet for office PCs, printers, and internal applications.

<img width="1417" height="551" alt="image" src="https://github.com/user-attachments/assets/62eb8fe6-f542-4887-9d6c-22f6456a290c" />

We will use the Case B for subnetting (requirement is number of hosts/subnet), in which we work first with the network with the highest number of hosts which is in the network design that is Prod1 followed by Prod2 and Prod3 then lastly Administration Department.

---
### For Prod1 Department
### Steps

### 1. Identify the Default Class of the Network IP
- Given IP: Class C  

### 2. Identify the Default Subnet Mask
- Class C → `/24` → `255.255.255.0`

### 3. Network ID
- Representation: `N.N.N.H`  
- Binary mask: ![Red](https://img.shields.io/badge/1111%201111.1111%201111.1111%201111-red)
![Blue](https://img.shields.io/badge/.0000%200000-blue)
- Host bits available: 8  
- Requirement: 60 hosts (Prod1 Department)  
- Formula:
  - \(x_h = (2^h - 2)\) ≥ required hosts
  - Host bits needed: 6 host bits (movement of bits is from RIGHT to LEFT)  
  - Hosts/subnet: \(x_h = (2^6 - 2)\) = 62 usable hosts/subnet
- Remaining bit(s): Host bits available - Host bits needed = 8 - 6 = 2 (will become network bit `n`)
  - Subnets: \(2^n = 2^2 = 4\) subnets generated
- New Binary Mask: ![Red](https://img.shields.io/badge/1111%201111.1111%201111.1111%201111.11-red)
![Blue](https://img.shields.io/badge/00%200000-blue)

### 4. Customized Subnet Mask
- New mask: `/24 + 2 = /26`  
- Equivalent: `255.255.255.192`

### 5. IP Range
- Block size: \(256 - 192 = 64\)
- Add the value `64` to the recent modified octet. In our case, the recent modified octet is the 4th octet.

### 6. Subnet Table
- The 1st subnet is the provided network IP address then we add the block size to the 4th octet.
- When working with the subnet table, do TOP to BOTTOM approach starting from the network IP address then broadcast IP address and then lastly, the Range of usable host IPs.

| Subnet No. | Network IP Address | Range of Usable Host IPs       | Broadcast IP Address | Assignment       |
|------------|--------------------|--------------------------------|----------------------|------------------|
| 1          | 192.168.0.0       | 192.168.0.1 – 192.168.0.62   | 192.168.0.63     | Prod1 Department       |
| 2          | 192.168.0.64       | 192.168.0.65 – 192.168.0.126   | 192.168.0.127     | Use for VLSM (Prod2 and Prod3)       |
| 3          | 192.168.0.128       | 192.168.0.129 – 192.168.0.190   | 192.168.0.191     | Use for VLSM (Administration)       |
| 4          | 192.168.0.192       | 192.168.0.193 – 192.168.0.254   | 192.168.0.255     | Reserve      |

<p align="justify">
The 1st subnet (192.168.0.0/26) will be used for Prod1 department and we will now proceed for Prod2 and Prod3 since they both have the same requirement which is 30 hosts.
</p> 

### Assigning the network IP to Prod1 Department

<img width="1416" height="566" alt="image" src="https://github.com/user-attachments/assets/549baa30-f48c-4011-b517-9ade7a119283" />


---
### For Prod2 and Prod3 Department
### VLSM (Variable Length Subnet Masking)
Let us now work on with subnetting the 2nd subnet (192.168.0.64/26) base on the number of hosts required (30 hosts) for both Prod2 and Prod3 department. These means we need two networks that has 30 hosts/network.
 
### Steps

### 1. Identify the Default Class of the Network IP (192.168.0.64/26)
- Given IP: Class C

### 2. Identify the Default Subnet Mask
- Class C → `/24` → `255.255.255.0`

### 3. Network ID of the provided Network IP
- Representation: `N.N.N.nnhh hhhh`  
- Binary mask: ![Red](https://img.shields.io/badge/1111%201111.1111%201111.1111%201111.11-red)
![Blue](https://img.shields.io/badge/00%200000-blue)
- Host bits available: 6
- Requirement: 30 hosts (Prod2 and Prod3)  
- Formula:
  - \(x_h = (2^h - 2)\) ≥ required hosts
  - Host bits needed: 5 host bits (movement of bits is from RIGHT to LEFT)  
  - Hosts/subnet: \(x_h = (2^5 - 2)\) = 30 usable hosts/subnet
- Remaining bit(s): Host bits available - Host bits needed = 6 - 5 = 1 (will become network bit `n`)
  - Subnets: \(2^n = 2^1 = 2\) subnets generated
- New Binary Mask: ![Red](https://img.shields.io/badge/1111%201111.1111%201111.1111%201111.111-red)
![Blue](https://img.shields.io/badge/0%200000-blue)
### 4. Customized Subnet Mask
- New mask: `/26 + 1 = /27`  
- Equivalent: `255.255.255.224`
- Originally `/24` and there is now 3 new network bits including the first subnetting we performed. We can use the subnet mask reference below to get the equivalent decimal value of the modified octet.

  ## Subnet Mask Reference

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
- Block size: \(256 - 224 = 32\)
- Add the value `32` to the recent modified octet. In our case, the recent modified octet is the 4th octet.

### 6. Subnet Table
- The 1st subnet is the provided network IP address (192.168.0.64/27) then we add the block size to the 4th octet.
- When working with the subnet table, do TOP to BOTTOM approach starting from the network IP address then broadcast IP address and then lastly, the Range of usable host IPs.


| Subnet No. | Network IP Address | Range of Usable Host IPs       | Broadcast IP Address | Assignment       |
|------------|--------------------|--------------------------------|----------------------|------------------|
| 1          | **192.168.0.64**       | 192.168.0.65 – 192.168.0.94   | 192.168.0.95     | Prod2 Department       |
| 2          | 192.168.0.96       | 192.168.0.97 – 192.168.0.126   | **192.168.0.127**     | Prod3 Department       |


To verify if we got the correct subnet table, refer to the network IP address (192.168.0.64) and broadcast IP address (192.168.0.127) in the Prod1 subnet table. In the new subnet table above, the 1st network IP address and last broadcast IP address have matched with our reference.
<br>
We can now assign a network to Prod2 and Prod3.
<img width="1433" height="572" alt="image" src="https://github.com/user-attachments/assets/59edc806-7c51-41eb-970f-a62679d902d6" />

---
### For Administration Department
Since there is no longer available network (from the subnet of Prod2 and Prod3), we will go back to the Prod1 subnet table in which there are two networks that are available: 192.168.0.128/26 and 192.168.0.192/26. We can select any of these two available networks. Let's say we use 192.168.0.128/26.

### Steps

### 1. Identify the Default Class of the Network IP (192.168.0.128/26)
- Given IP: Class C

### 2. Identify the Default Subnet Mask
- Class C → `/24` → `255.255.255.0`

### 3. Network ID of the provided Network IP
- Representation: `N.N.N.nnhh hhhh`  
- Binary mask: ![Red](https://img.shields.io/badge/1111%201111.1111%201111.1111%201111.11-red)
![Blue](https://img.shields.io/badge/00%200000-blue)
- Host bits available: 6
- Requirement: 10 hosts
- Formula:
  - \(x_h = (2^h - 2)\) ≥ required hosts
  - Host bits needed: 4 host bits (movement of bits is from RIGHT to LEFT)  
  - Hosts/subnet: \(x_h = (2^4 - 2)\) = 16 usable hosts/subnet
- Remaining bit(s): Host bits available - Host bits needed = 6 - 4 = 2 (will become network bit `n`)
  - Subnets: \(2^n = 2^2 = 4\) subnets generated
- New Binary Mask: ![Red](https://img.shields.io/badge/1111%201111.1111%201111.1111%201111.1111-red)
![Blue](https://img.shields.io/badge/%200000-blue)
### 4. Customized Subnet Mask
- New mask: `/26 + 2 = /28`  
- Equivalent: `255.255.255.240`
- Originally `/24` and there is now 4 new network bits including the first subnetting we performed. We can use the subnet mask reference below to get the equivalent decimal value of the modified octet.

  ## Subnet Mask Reference

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
- Block size: \(256 - 240 = 16\)
- Add the value `16` to the recent modified octet. In our case, the recent modified octet is the 4th octet.

### 6. Subnet Table
- The 1st subnet is the provided network IP address (192.168.0.128/26) then we add the block size to the 4th octet.
- When working with the subnet table, do TOP to BOTTOM approach starting from the network IP address then broadcast IP address and then lastly, the Range of usable host IPs.


| Subnet No. | Network IP Address | Range of Usable Host IPs       | Broadcast IP Address | Assignment       |
|------------|--------------------|--------------------------------|----------------------|------------------|
| 1          | **192.168.0.128**       | 192.168.0.129 – 192.168.0.142   | 192.168.0.143     | Administration Department       |
| 2          | 192.168.0.144       | 192.168.0.145 – 192.168.0.158   | 192.168.0.159     | Reserve      |
| 3          | 192.168.0.160       | 192.168.0.161 – 192.168.0.174   | 192.168.0.175     | Reserve      |
| 4          | 192.168.0.176       | 192.168.0.177 – 192.168.0.190   | **192.168.0.191**     | Reserve      |

<img width="1428" height="568" alt="image" src="https://github.com/user-attachments/assets/808c52ff-1be8-4ead-b88c-79a45f62d3c9" />

[Class B Subnetting](../class-b/README.md)


---

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br> [Next →](../02-lan-technologies/README.md)
