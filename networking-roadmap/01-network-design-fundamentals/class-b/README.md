[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br> [Next →](../02-lan-technologies/README.md)

## Case B Scenario <br>
**Company**: EduTech Manufacturing Corp.  <br>
**Industry**: Educational equipment and technology solutions<br>
They have two major departments that need separate networks:

**Production Department**<br>
Handles manufacturing of school supplies and tech devices.<br>
Requires a large subnet for factory machines, IoT sensors, and production servers.

**Administration Department**<br>
Includes HR, Finance, and Management offices.<br>
Needs a smaller subnet for office PCs, printers, and internal applications.

<img width="655" height="333" alt="image" src="https://github.com/user-attachments/assets/f6f5bb9a-fb11-4592-a26e-1d1949e780fd" />

For Case B in subnetting (requirement is number of hosts/subnet), we will work first with the network with the highest number of hosts which is in the network design that is the *Production Department*.

### Steps

### 1. Identify the Default Class of the Network IP
- Given IP: Class B  
- Default subnet mask (SM): `255.255.0.0` or `/16`

### 2. Identify the Default Subnet Mask
- Class B → `/16` → `255.255.0.0`

### 3. Network ID
- Representation: `N.N.H.H`  
- Binary mask: ![Red](https://img.shields.io/badge/1111%201111.1111%201111-red)
![Blue](https://img.shields.io/badge/.0000%200000.0000%200000-blue)
- Host bits available: 16  
- Requirement: 20,000 hosts (Production Department)  
- Formula:
  - \(x_h = (2^h - 2)\) ≥ required hosts
  - Host bits needed: 15 host bits (movement of bits is from RIGHT to LEFT)  
  - Hosts/subnet: \(x_h = (2^15 - 2)\) = 32,676 usable hosts/subnet
- Remaining bit(s): Host bits available - Host bits needed = 16 -15 = 1 (will become network bit `n`)
  - Subnets: \(2^n = 2^1 = 2\) subnets generated
- New Binary Mask: ![Red](https://img.shields.io/badge/1111%201111.1111%201111.1-red)
![Blue](https://img.shields.io/badge/000%200000.0000%200000-blue)

### 4. Customized Subnet Mask
- New mask: `/16 + 1 = /17`  
- Equivalent: `255.255.128.0`

### 5. IP Range
- Block size: \(256 - 128 = 128\)
- Add the value `128` to the recent modified octet. In our case, the recent modified octet is the 3rd octet.

### 6. Subnet Table
- The 1st subnet is the provided network IP address then we add the block size to the 3rd octet.
- When working with the subnet table, do TOP to BOTTOM approach starting from the network IP address then broadcast IP address and then lastly, the Range of usable host IPs.

| Subnet No. | Network IP Address | Range of Usable Host IPs       | Broadcast IP Address |
|------------|--------------------|--------------------------------|----------------------|
| 1          | 128.0.**0**.0         | 128.0.0.1 – 128.0.127.254      | 128.0.**127**.255     |
| 2          | 128.0.**128**.0       | 128.0.128.1 – 128.0.255.254    | 128.0.**255**.255     |

<p align="justify">
We will use the 1st subnet for the Production Department and we can use the 2nd subnet for the Administration Department. But if we are to evaluate the number of required hosts for the Administration department, it only requires 5,000 hosts and the 2nd subnet has 32,676 hosts. There are approximately 27,000 hosts wasted. In order to efficiently use the 2nd network, we can subdivide it according to what is required. In which, we are now gonna be working with VLSM (Variable Length Subnet Masking).
</p> 

### Assigning the network IP to Production Department

<img width="661" height="337" alt="image" src="https://github.com/user-attachments/assets/b35ccaab-4568-47e4-a49c-c4d1074c5a46" />


## VLSM (Variable Length Subnet Masking)
Let us now work on with subnetting the 2nd subnet (128.0.128.0/17) base on the number of hosts required (5,000 hosts) for Administration Department.
 
### Steps

### 1. Identify the Default Class of the Network IP (128.0.128.0/17)
- Given IP: Class B (It is not class C since it is not beyond /24)
- Default subnet mask (SM): `255.255.0.0` or `/16`

### 2. Identify the Default Subnet Mask
- Class B → `/16` → `255.255.0.0`

### 3. Network ID of the provided Network IP
- Representation: `N.N.nhhh hhhh.hhhh hhhh`  
- Binary mask: ![Red](https://img.shields.io/badge/1111%201111.1111%201111.1-red)
![Blue](https://img.shields.io/badge/000%200000.0000%200000-blue)
- Host bits available: 15  
- Requirement: 5,000 hosts (Administration Department)  
- Formula:
  - \(x_h = (2^h - 2)\) ≥ required hosts
  - Host bits needed: 13 host bits (movement of bits is from RIGHT to LEFT)  
  - Hosts/subnet: \(x_h = (2^13 - 2)\) = 8,190 usable hosts/subnet
- Remaining bit(s): Host bits available - Host bits needed = 15 - 13 = 2 (will become network bit `n`)
  - Subnets: \(2^n = 2^2 = 4\) subnets generated
- New Binary Mask: ![Red](https://img.shields.io/badge/1111%201111.1111%201111.111-red)
![Blue](https://img.shields.io/badge/0%200000.0000%200000-blue)

### 4. Customized Subnet Mask
- New mask: `/17 + 2 = /19`  
- Equivalent: `255.255.224.0`
- Originally `/16` and there is now 3 new network bits including the first subnetting we performed. We can use the subnet mask reference below to get the equivalent decimal value of the modified octet.

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
- Add the value `32` to the recent modified octet. In our case, the recent modified octet is the 3rd octet.

### 6. Subnet Table
- The 1st subnet is the provided network IP address then we add the block size to the 3rd octet.
- When working with the subnet table, do TOP to BOTTOM approach starting from the network IP address then broadcast IP address and then lastly, the Range of usable host IPs.

| Subnet No. | Network IP Address | Range of Usable Host IPs       | Broadcast IP Address |
|------------|--------------------|--------------------------------|----------------------|
| 1          | 128.0.**128**.0       | 128.0.0.1 – 128.0.159.254      | 128.0.**159**.255     |
| 2          | 128.0.**160**.0       | 128.0.160.1 – 128.0.191.254    | 128.0.**191**.255     |
| 3          | 128.0.**192**.0       | 128.0.192.1 – 128.0.223.254    | 128.0.**223**.255     |
| 4          | 128.0.**224**.0       | 128.0.224.1 – 128.0.255.254    | 128.0.**255**.255     |


We can now assign a network to the **Administration Department**. Let us assign the 1st subnet **128.0.128.0/19**. 
<br>
<img width="664" height="344" alt="image" src="https://github.com/user-attachments/assets/dd6281e1-449d-48d1-b7f7-2fb5af1af34f" />




The remaining subnets (no. 3 and 4) will be reserved in case the company wants to expand and in which this design makes the company scalable but take note that these reserve networks can only cater 8,190 hosts/network. <br>

If you wish to learn more about subnetting specifically for class C, you may proceed to Class C Subnetting (click the button below).
<br>

[Class C Subnetting](../class-c/README.md)


---

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br> [Next →](../02-lan-technologies/README.md)
