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


### VLAN Configuration
On both switches, configure the VLANs.
```text
VLAN10 (Sales)        S1: F0/2, S2: F0/2
VLAN20 (Billing)      S1: F0/3, S2: F0/3
VLAN30 (HR)           S2: F0/4
VLAN99 (Security)     All unused ports (shutdown)
VLAN100 (Management)  Po1 (S1: F0/1 & F0/24, S2: F0/1 & F0/24)
```
<img width="1425" height="261" alt="image" src="https://github.com/user-attachments/assets/da519e59-8bba-48b7-bcf2-4ae01ff5333e" />

Activate the VLANS on both switches
```bash
interface vlan 10
interface vlan 20
interface vlan 30
interface vlan 100
```

#### Interface Assignment (Access and Trunk)
```text
VLAN10 (Sales)        S1: F0/2, S2: F0/2                                         Access
VLAN20 (Billing)      S1: F0/3, S2: F0/3                                         Access
VLAN30 (HR)           S2: F0/4                                                   Access
VLAN99 (Security)     All unused ports (shutdown)                                Shutdown
VLAN100 (Management)  Po1 (S1: F0/1 & F0/24, S2: F0/1 & F0/24), S1 and S2: G0/1  Trunk
```
<img width="668" height="627" alt="image" src="https://github.com/user-attachments/assets/e64e2c9a-9a46-4988-a8b8-a286916b5f3f" />

<img width="669" height="476" alt="image" src="https://github.com/user-attachments/assets/f0ddbe09-d9f5-416b-ad68-c596c4a22363" />

<img width="1125" height="72" alt="image" src="https://github.com/user-attachments/assets/79c0d46c-3645-44c1-af4d-5457dba31071" />

We will only allow VLAN 10, 20, 30, and 100.

<img width="1176" height="315" alt="image" src="https://github.com/user-attachments/assets/22a5a0a3-ef0d-4323-8986-077e829e6cc0" />


### Etherchannel Configuration
On both switches, configure the etherchannel (Port-channel 1). We will use LACP.
```bash
int range f0/1,f0/24
  channel-group 1 mode active

int po1
  switchport mode trunk
  switchport trunk native vlan 100
  switchport trunk allowed vlan 10,20,30,100
```
<img width="1499" height="463" alt="image" src="https://github.com/user-attachments/assets/edb62df9-6405-4be1-b9d0-981430ec010e" />

### STP Configuration
Enable rapid-pvst on both switches.
```text
spanning-tree mode rapid-pvst
```
<img width="1413" height="190" alt="image" src="https://github.com/user-attachments/assets/d698a8d7-827b-4be7-beb5-2c21388f7ca2" />

Enable portfast on connections to end devices. 
```text
S1: F0/2, F0/3
S2: F0/2, F0/3, F0/4
```
```bash
Configure on S1
interface range f0/2-3
spanning-tree portfast

Configure on S2
interface range f0/2-4
spanning-tree portfast
```
### Router Configuration (Inter-VLAN, HSRP, IP and Routing)

```text
ARtr (Active Router)
g0/0.10	192.168.10.1/24
g0/0.20	192.168.20.1/24
g0/0.30	192.168.30.1/24
g0/0.100	192.168.100.1/24
s0/0/0	200.0.2.1/30

SRtr (Standby Router)
g0/0.10	192.168.10.2/24
g0/0.20	192.168.20.2/24
g0/0.30	192.168.30.2/24
g0/0.100	192.168.100.2/24
s0/0/0	200.0.1.1/30

For HSRP, the same information below will be used for HSRP. 
HSRP
VRIP (VLAN 10)		192.168.10.11/24
VRIP (VLAN 20)		192.168.20.12/24
VRIP (VLAN 30)		192.168.30.13/24
VRIP (VLAN 100)	192.168.100.10/24
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
Upon proper evaluation, if the connection between either ARtr and the ISP or SRtr and the ISP fails, the local network will not be able to connect to the internet, unless the connection between ARtr and S1 fails, because that is the only time that SRtr will become the **active router** and the local network can access the internet. Therefore, I have decided to add a connection between ARtr and SRtr in order for the local network to still connect to the internet.

<img width="1401" height="647" alt="image" src="https://github.com/user-attachments/assets/732f39fc-aceb-4d8f-8d5b-9b1a08269b3f" />

I have added another static route between ARtr and SRtr
```bash
ARtr
ip route 0.0.0.0 0.0.0.0 g0/1 10.0.0.2
SRtr
ip route 0.0.0.0 0.0.0.0 g0/1 10.0.0.1
```
