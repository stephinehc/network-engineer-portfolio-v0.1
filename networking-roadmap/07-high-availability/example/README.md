[High Availability (HSRP)](../README.md)

---

# ABC Technologies – Network Design Project

## Project Background

ABC Technologies is expanding its operations by opening a new **Administration & Corporate Services** office separate from its existing office at a different location. As our company continues to grow, we require a reliable and secure network that allows employees from both locations to communicate efficiently and access company resources without interruption.

As the assigned **Network Engineer**, you are responsible for designing and implementing a network solution that fulfills the following business requirements.

---

# Company Requirements

## 1. Department Organization

- Organize the Sales, Billing, and Human Resources departments into separate network groups to improve organization and security.
- Determine the most appropriate design for each department.

---

## 2. Communication

- Ensure employees from all departments can communicate and collaborate whenever necessary.
- Connect both office locations so they can operate as a single organization.

---

## 3. Reliability

- Design the network to continue operating even if a network device or connection becomes unavailable.
- Minimize disruptions to daily business operations.

---

## 4. Performance

- Design the network to provide fast and reliable communication between devices.
- Improve the efficiency of connections between networking equipment.

---

## 5. Security

- Disable or secure any network connections that are not being used.
- Follow industry best practices to improve the overall security of the company's network.

---

## 6. Internet and Company Website

- Provide Internet access for employees at both office locations.
- Ensure all employees can access the company's website using **http://abc-tech1.com**.
- The company website and its supporting services are hosted on the Internet.

---

## 7. Verification

Upon completion of the project, demonstrate that:

- Employees can communicate within and between departments as required.
- Both office locations can communicate successfully.
- The network remains operational even if a device or connection fails.
- Employees can access the Internet and successfully open **http://abc-tech1.com**.

---
## Network Design
<img width="1476" height="648" alt="image" src="https://github.com/user-attachments/assets/6cffa2e2-fdf7-481c-939d-f931656b7632" />


### VLAN Configuration and Interface Assignment (Access and Trunk)
On both switches, configure the VLANs.
```text
VLAN10 (Sales)        S1: F0/1, S2: F0/1
VLAN20 (Billing)      S1: F0/2, S2: F0/2
VLAN30 (HR)           S2: F0/3
VLAN99 (Security)     All unused ports (shutdown)
VLAN100 (Management)  Po1 (S1: F0/23 & F0/24, S2: F0/23 & F0/24)
```

Activate the VLANS on both switches
```bash
interface vlan 10
interface vlan 20
interface vlan 30
interface vlan 100
```
Assignment of Ports
```text
VLAN10 (Sales)        S1: F0/1, S2: F0/1                                         Access
VLAN20 (Billing)      S1: F0/2, S2: F0/2                                         Access
VLAN30 (HR)           S2: F0/3                                                   Access
VLAN99 (Security)     All unused ports (shutdown)                                Shutdown
VLAN100 (Management)  Po1 (S1: F0/23 & F0/24, S2: F0/1 & F0/24), S1 and S2: G0/1  Trunk
```
<img width="1479" height="756" alt="image" src="https://github.com/user-attachments/assets/b252e80b-eeb0-4ecb-9add-932cd498214c" />


### Etherchannel Configuration
On both switches, configure the etherchannel (Port-channel 1). We will use PAGP.
```bash
int range f0/23,f0/24
  channel-group 1 mode desirable

int po1
  switchport mode trunk
  switchport trunk native vlan 100
  switchport trunk allowed vlan 10,20,30,100
```
<img width="1377" height="734" alt="image" src="https://github.com/user-attachments/assets/da25df73-1688-4688-b996-bbde35cf3ed4" />


### STP Configuration
Enable rapid-pvst on both switches.
```text
spanning-tree mode rapid-pvst
```
<img width="1413" height="190" alt="image" src="https://github.com/user-attachments/assets/d698a8d7-827b-4be7-beb5-2c21388f7ca2" />

Enable portfast on connections to end devices. 
```text
S1: F0/1, F0/
S2: F0/1, F0/2, F0/3
```
```bash
Configure on S1
interface range f0/1-2
spanning-tree portfast

Configure on S2
interface range f0/1-3
spanning-tree portfast
```
### Router Configuration (Inter-VLAN, HSRP, IP and Routing)

```text
ARtr (Active Router)
g0/0.10	  192.168.10.1/24
g0/0.20	  192.168.20.1/24
g0/0.30	  192.168.30.1/24
g0/0.100	192.168.100.1/24
s0/0/0	  200.0.2.1/30

SRtr (Standby Router)
g0/0.10   192.168.10.2/24
g0/0.20   192.168.20.2/24
g0/0.30   192.168.30.2/24
g0/0.100  192.168.100.2/24
s0/0/0	  200.0.1.1/30

For HSRP, the same information below will be used for HSRP. 
HSRP
VRIP (VLAN 10)		192.168.10.11/24
VRIP (VLAN 20)		192.168.20.12/24
VRIP (VLAN 30)		192.168.30.13/24
VRIP (VLAN 100)	  192.168.100.10/24
```
We will use the following information above for the aforementioned configurations. ARtr will have higher priority(150) to become the active router and SRtr will have lower priority(50) to become the standby router.

ARtr configuration
```bash
int g0/0.10
  encapsulation dot1q 10
  ip address 192.168.10.1 255.255.255.0
  standby 1 ip 192.168.10.11
  standby 1 priority 150
  standby 1 preempt
int g0/0.20
  encapsulation dot1q 20
  ip address 192.168.20.1 255.255.255.0
  standby 2 ip 192.168.20.12
  standby 2 priority 150
  standby 2 preempt
int g0/0.30
  encapsulation dot1q 30
  ip address 192.168.30.1 255.255.255.0
  standby 3 ip 192.168.30.13
  standby 3 priority 150
  standby 3 preempt
int g0/0.100
  encapsulation dot1q 100
  ip address 192.168.100.1 255.255.255.0
  standby 4 ip 192.168.100.10
  standby 4 priority 150
  standby 4 preempt
int g0/0
  no shutdown
int s0/0/0
  ip address 200.0.2.1 255.255.255.252
  no shutdown
ip route 0.0.0.0 0.0.0.0 s0/0/0 200.0.2.2

```

SRtr configuration
```bash
int g0/0.10
  encapsulation dot1q 10
  ip address 192.168.10.2 255.255.255.0
  standby 1 ip 192.168.10.11
  standby 1 priority 50
  standby 1 preempt
int g0/0.20
  encapsulation dot1q 20
  ip address 192.168.20.2 255.255.255.0
  standby 2 ip 192.168.20.12
  standby 2 priority 50
  standby 2 preempt
int g0/0.30
  encapsulation dot1q 30
  ip address 192.168.30.2 255.255.255.0
  standby 3 ip 192.168.30.13
  standby 3 priority 50
  standby 3 preempt
int g0/0.100
  encapsulation dot1q 100
  ip address 192.168.100.2 255.255.255.0
  standby 4 ip 192.168.100.10
  standby 4 priority 50
  standby 4 preempt
int g0/0
  no shutdown
int s0/0/0
  ip address 200.0.1.1 255.255.255.252
  no shutdown
ip route 0.0.0.0 0.0.0.0 s0/0/0 200.0.1.2
```
## Network Re-design
Upon proper evaluation, if the connection between either ARtr and the WAN or SRtr and the WAN fails, the local network will not be able to connect to the internet, unless the connection between ARtr and S1 fails, because that is the only time that SRtr will become the **active router** and the local network can access the internet. Therefore, I have decided to add a connection between ARtr and SRtr in order for the local network to still connect to the internet.

<img width="1246" height="661" alt="image" src="https://github.com/user-attachments/assets/a3c868a1-f476-48af-847f-d0d631c2a4d6" />


I have added another static route between ARtr and SRtr
```bash
ARtr
ip route 0.0.0.0 0.0.0.0 g0/1 10.0.0.2
SRtr
ip route 0.0.0.0 0.0.0.0 g0/1 10.0.0.1
```
## Verification
### Communication
  - Ensure employees from all departments can communicate and collaborate whenever necessary.
  - Connect both office locations so they can operate as a single organization.


**Location A**

<img width="1246" height="661" alt="image" src="https://github.com/user-attachments/assets/c20e9b3e-b168-492f-b52e-8868ee186416" />

**Location B**
<img width="1564" height="662" alt="image" src="https://github.com/user-attachments/assets/02f7efec-2a9a-4b0f-a37b-25ea943c3897" />

#### Communication of Location A (LAN)
```text
PC
VLAN 10 (Sales)
Bob	  192.168.10.254/24
Matt	192.168.10.253/24
VLAN 20 (Billing)
Alice	192.168.20.254/24
Don	  192.168.20.253/24
VLAN 30 (HR)
Cath	192.168.30.254/24
```
We will use Bob's PC to verify communication to all PC.
<img width="833" height="979" alt="image" src="https://github.com/user-attachments/assets/9a95e7c8-26aa-48e8-9ee9-d328df1f97c4" />

Bob is able to communicate with Matt (192.168.10.253), Alice (192.168.20.254), Don (192.168.20.253), and Cath (192.168.30.254)

#### Communication from Location A to Location B
For location B, we will communicate Network A (192.168.0.1) and Network B (192.168.1.1).

We will use Alice's PC to verify connectivity to Location B. Multiple attempts was done to ping Network A and Network B to achieve 0% loss.
<img width="882" height="619" alt="image" src="https://github.com/user-attachments/assets/df62c453-e06a-4bba-95de-7e3b429e2e51" />

### Fault Tolerance/Reliability
Let us consider that the link between either ARtr and WAN or SRtr and WAN fail. To simulate this, we will remove the link.

Scenario 1: Link failure between ARtr and WAN

Verify connection between Location A and Location B. We will still use Alice's PC to ping Network A and Network B.

<img width="1920" height="933" alt="image" src="https://github.com/user-attachments/assets/519061b1-0d86-4dfb-93bd-3f2a14493004" />

Scenario 2: Link failure between SRtr and WAN
Verify connection between Location A and Location B. We will still use Alice's PC to ping Network A and Network B.

<img width="1920" height="942" alt="image" src="https://github.com/user-attachments/assets/3a3ff340-d2ac-4c13-a95d-c82a1871ef5b" />

#### HSRP verification
Let us conside the link of ARtr and the switch failed, SRtr should become the **ACTIVE ROUTER**. To simulate link failure, we will remove the link of ARtr and the switch. Then, we will verify if we still have connectivity in the local network and to location B. We will use Matt's PC to verify connectivity.

SRtr is now the Active Router
<img width="1696" height="803" alt="image" src="https://github.com/user-attachments/assets/5454417e-04d1-42e0-8aa5-d22b8ebd15f3" />

**Connectivity**
<img width="1558" height="879" alt="image" src="https://github.com/user-attachments/assets/f7fcb52b-ba37-4c5c-bc84-4f4e545a3139" />

### Company Website
<img width="1920" height="897" alt="image" src="https://github.com/user-attachments/assets/9fc80aee-3ba3-481d-9d5f-428f8d1ba2a0" />

---
[High Availability (HSRP)](../README.md)
