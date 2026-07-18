
[Network Services](../../README.md)

---

# Network Services
## DHCP, DNS, and Web Server Configuration

---

# Scenario

ABC Technologies is deploying a small enterprise network consisting of two office LANs connected through an Edge Router. The company wants to implement centralized name resolution and internal web hosting while demonstrating two different DHCP deployment methods.

The organization consists of two departments:

- **Network A**
  - Uses a **dedicated DHCP server**.
  - All client computers automatically obtain their IP configuration from the DHCP server.

- **Network B**
  - Uses the **DHCP service configured on the Edge Router**.
  - Client computers automatically obtain their IP configuration from the router.

Both LANs connect to an **Edge Router**, which provides routing between all networks. The Edge Router is connected to a separate router (or server network) containing:

- One DNS Server
- Three Web Servers

The DNS server provides name resolution for the company's internal websites.

Employees should be able to access the following websites using their domain names instead of IP addresses:

- **itn.com**
- **srwe.com**
- **ensa.com**

---
Network Topology and IP assignment
<img width="830" height="644" alt="image" src="https://github.com/user-attachments/assets/34a24964-3c3b-4d58-89a0-72be25f72cbb" />

<img width="779" height="573" alt="image" src="https://github.com/user-attachments/assets/7454d4b3-0270-4068-95bc-478679068684" />

## Dedicated DHCP server/TFTP server configuration
### DHCP server
Under Services Tab, we will select DHCP and configure it using the provided information.
  - Click the serverPool and fill-in the following informations.

  <img width="863" height="484" alt="image" src="https://github.com/user-attachments/assets/271dcba8-1f0d-4740-83da-ac3e896abb65" />

  - Afterwards, select the Desktop Tab and configure the IP of the server.

  <img width="873" height="517" alt="image" src="https://github.com/user-attachments/assets/53c4aaba-7f05-4656-b320-cd38e66e0cb3" />

### TFTP server
Under Services Tab, select ON. Right now, there are no files saved yet.
<img width="873" height="555" alt="image" src="https://github.com/user-attachments/assets/34fede14-2343-4f86-9108-f94f6dc7e928" />

## Configure the Edge Router
### IP configuration
```text
Edge Router
g0/0/0	192.168.0.1/24
g0/0/1	192.168.1.1/24
s0/1/0	10.0.0.1/30
```
<img width="849" height="632" alt="image" src="https://github.com/user-attachments/assets/7a120e4a-d005-498c-8c09-e375fa6e5794" />

### Configure Edge Router as DHCP
<img width="543" height="115" alt="image" src="https://github.com/user-attachments/assets/d6dc319c-d84b-492e-a930-f28555e97b7b" />

## DHCP verification
PC-A and PC-B has successfully acquired an IP address.
<img width="1063" height="555" alt="image" src="https://github.com/user-attachments/assets/5555f021-5b6f-40cf-9f72-c17d15c183d3" />

## DNS and Web Server Configuration
### IP Configuration
#### DNS Server
Under Desktop Tab, configure the IP.
<img width="873" height="561" alt="image" src="https://github.com/user-attachments/assets/36260074-e202-450e-a8ea-0d168c356c28" />

#### Web Server
Under Desktop Tab, configure the IP.
<img width="1919" height="578" alt="image" src="https://github.com/user-attachments/assets/9a55f012-a40d-477d-82d0-5589ae8cdce7" />

### Server Configuration
#### DNS Server
Under Servics Tab, select DNS > click On > add the following information.
<img width="871" height="403" alt="image" src="https://github.com/user-attachments/assets/4dad4da6-299b-4f92-b2ed-80c331e98daf" />

#### Web Server
Under Services Tab, select HTTP > click On for both HTTP and HTTPS > click import to add your own html file (optional).

Repeat this process to the other web servers (srwe.com and ensa.com)

<img width="637" height="697" alt="image" src="https://github.com/user-attachments/assets/804b7928-d732-41c5-aa99-4d3863c07142" />

## Routing Configuration for Edge and Server Router
We will use OSPF for the routing protocol.
<img width="1277" height="791" alt="image" src="https://github.com/user-attachments/assets/c00d8ad0-fcf7-4a39-b7df-562f2a698e28" />

## DNS Verification
```text
Web Servers
itn.com	  201.20.1.10/24
srwe.com	201.20.1.11/24
ensa.com	201.20.1.12/24
```
<img width="1647" height="966" alt="image" src="https://github.com/user-attachments/assets/a40d0283-4298-4d1f-86db-e1c6c16d8eb0" />

---

[Network Services](../05-network-services/README.md)

