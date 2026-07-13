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

### Steps

### 1. Identify the Default Class of the Network IP
- Given IP: Class C  
- Default subnet mask (SM): `255.255.255.0` or `/24`

### 2. Identify the Default Subnet Mask
- Class B → `/16` → `255.255.255.0`

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
| 2          | 192.168.0.64       | 192.168.0.65 – 192.168.0.126   | 192.168.0.127     |       |
| 3          | 192.168.0.128       | 192.168.0.129 – 192.168.0.190   | 192.168.0.191     |       |
| 4          | 192.168.0.192       | 192.168.0.193 – 192.168.0.254   | 192.168.0.255     |       |

<p align="justify">
The 1st subnet (192.168.0.0/26) will be used for Prod1 department and we will now proceed for Prod2 and Prod3 since they both have the same requirement which is 30 hosts.
</p> 

### Assigning the network IP to Prod1 Department

<img width="1416" height="566" alt="image" src="https://github.com/user-attachments/assets/549baa30-f48c-4011-b517-9ade7a119283" />



### VLSM (Variable Length Subnet Masking)
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

To verify if we got the correct subnet table, the recent modified octets of the last network IP address should match with the recent modified octet of the customized subnet mask. In this case: <br>
Last network IP address:  128.0.**224**.0 <br>
Customized subnet mask:  255.255.**224**.0 <br>
<br>
We can now assign a network to the **Administration Department**. Let us assign the 1st subnet **128.0.224.0/19**.
<img width="648" height="332" alt="image" src="https://github.com/user-attachments/assets/6bd7d036-a176-4c70-bb37-a445fdb758bc" />



The remaining subnets will be reserved in case the company wants to expand and in which this design makes the company scalable but take note that these reserve networks can only cater 8,190 hosts/network. <br>

If you wish to learn more about subnetting specifically for class C, you make proceed to Class C Subnetting (click the button below).
<br>

[Class C Subnetting](../class-c/README.md)

---

## Resources
- 📖 [Cisco Networking Academy: Subnetting](https://www.netacad.com/courses/networking)
- 📘 [RFC 950: Internet Standard Subnetting Procedure](https://www.rfc-editor.org/rfc/rfc950)
- 🧮 Online Subnet Calculator: [Subnetting Practice Tool](https://www.subnetting.net)
- 🎥 YouTube: “Subnetting Made Easy” tutorials

---

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br> [Next →](../02-lan-technologies/README.md)
