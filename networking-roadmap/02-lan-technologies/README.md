# 2. LAN Technologies (VLANs, Trunking, Inter-VLAN Routing)

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br> [← Previous](../01-network-design-fundamentals/README.md) · [Next →](../03-switching-technologies/README.md)

---

## Overview
<p align="justify">
This section covers the essential LAN technologies that enable segmentation, scalability, and efficient communication within enterprise networks. Virtual LANs (VLANs) provide logical separation of devices, trunking allows multiple VLANs to traverse a single link, and inter-VLAN routing ensures communication between VLANs. These concepts are foundational for secure and organized campus networks.
</p>

---

## Topics Covered
- VLAN concepts and configuration
- Benefits of VLANs (segmentation, security, efficiency)
- VLAN trunking using IEEE 802.1Q
- Native VLAN and VLAN tagging
- Inter-VLAN routing methods:
  - Router-on-a-Stick
  - Layer 3 Switch routing
- Common misconfigurations and troubleshooting

---

## Notes & Diagrams
## VLAN Concepts and Configuration

A **Virtual Local Area Network (VLAN)** is a logical grouping of devices within a switched network, regardless of their physical location. VLANs divide a single physical switch into multiple separate broadcast domains, allowing devices in the same VLAN to communicate as if they were connected to the same physical network.

### Basic VLAN Configuration (Cisco IOS)

### Create VLANs

<img width="1216" height="526" alt="image" src="https://github.com/user-attachments/assets/68a179f6-e8c0-4ff8-808b-dd288a01feb9" />

<br>

On both switches, enter the following commands: <br>
S1 and S2

```bash
Switch(config)# vlan 10
Switch(config-vlan)# name Sales

Switch(config)# vlan 20
Switch(config-vlan)# name Billing

Switch(config)# vlan 100
Switch(config-vlan)# name Management
```

Activate the VLANs

```bash
Switch(config)# interface vlan 10
Switch(config)# interface vlan 20
Switch(config)# interface vlan 100
```
<img width="954" height="710" alt="image" src="https://github.com/user-attachments/assets/9db68f28-c946-4211-8503-580870f6927d" />

<br>

**Assign an IP address to a VLAN** 
  - We assign an ip address to an SVI for management purposes such as for remote access. The IP address will serve as a default gateway for access. <br>
Base on the design, VLAN 100 has an assigned IP address which is for S1: 128.0.0.254/16 and S2: 128.0.0.253/16. <br>
IP address assignment on S1

```bash
Switch(config)# interface vlan 100
Switch(config-if)# ip address 128.0.0.254 255.255.0.0
Switch(config-if)# no shutdown
```

<img width="758" height="363" alt="image" src="https://github.com/user-attachments/assets/6f3033c8-3a31-4c21-a1d6-6f0fd86f81d3" />

<br>
IP address assignment on S2

```bash
Switch(config)# interface vlan 100
Switch(config-if)# ip address 128.0.0.253 255.255.0.0
Switch(config-if)# no shutdown
```
<img width="755" height="367" alt="image" src="https://github.com/user-attachments/assets/e7284454-e7c0-4d38-b238-288e27b92a4f" />



### Assign a Port to a VLAN
Base on the example topology we have, the following ports (f0/22, f0/23, f0/24) for S1 will be access ports and on S2 (f0/22, f0/23, f0/24) will be access ports too. Assign each port to their assigned VLAN.

```bash
Switch(config)# interface f0/22
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10

Switch(config)# interface f0/23
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10

Switch(config)# interface f0/24
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20
```
<img width="620" height="485" alt="image" src="https://github.com/user-attachments/assets/224c04d0-0863-471a-9313-f44d26d31507" />


### Verify VLANs

```bash
Switch# show vlan brief
```
<img width="760" height="363" alt="image" src="https://github.com/user-attachments/assets/b79f32a7-9a6b-4c0e-b12f-c23fb5160046" />

<br>
We can now see that the ports f0/22, f0/23, and f0/24 are now moved to their assigned VLANs.

---

## Benefits of VLANs

### 1. Segmentation

  - Divides a large network into smaller logical networks.
  - Limits broadcast traffic within each VLAN.
  - Improves network organization.

  **Example:**

  - VLAN 10 – Faculty
  - VLAN 20 – Students
  - VLAN 30 – Administration

---

### 2. Security

  - Prevents users in different VLANs from communicating directly.
  - Sensitive departments (e.g., Finance) can be isolated.
  - Communication between VLANs requires routing and can be controlled using Access Control Lists (ACLs).

---

### 3. Efficiency

  - Reduces unnecessary broadcast traffic.
  - Improves switch performance.
  - Makes network management easier.
  - Allows easier moves, adds, and changes without rewiring.

---


## VLAN Trunking Using IEEE 802.1Q

A **trunk link** carries traffic for **multiple VLANs** between switches or between a switch and a router.

IEEE **802.1Q** is the industry-standard trunking protocol.

Instead of using separate cables for every VLAN, a single trunk cable carries traffic for multiple VLANs simultaneously.

### Example Topology

<img width="846" height="515" alt="image" src="https://github.com/user-attachments/assets/c389a7f4-0796-4c85-a3f1-646696bef02d" />


### Configure a Trunk Port

Base on the figure above, configue both interface of S1 and S2 using the commands below.

```bash
Switch(config)# interface GigabitEthernet0/1
Switch(config-if)# switchport mode trunk
```
<img width="811" height="455" alt="image" src="https://github.com/user-attachments/assets/7ca9d569-961b-43ba-8550-bcdc40265f86" />
<img width="622" height="472" alt="image" src="https://github.com/user-attachments/assets/d4da4b7f-4e8d-4bcf-837d-8168f724a89d" />


#### Allow Specific VLANs (Optional)

```bash
Switch(config-if)# switchport trunk allowed vlan 10,20,100
```

#### Verify Trunk Configuration

```bash
Switch# show interfaces trunk
```
<img width="818" height="462" alt="image" src="https://github.com/user-attachments/assets/80c3d75c-038c-47bb-a63d-3a9a7e9cd3ae" />
<img width="850" height="467" alt="image" src="https://github.com/user-attachments/assets/b25bf387-3835-4d31-9013-2a600daf2634" />

As we can see on both switches (S1 and S2), the status of the interface g0/1 are now on trunking.


---

# Native VLAN and VLAN Tagging

## VLAN Tagging

When a frame crosses a trunk link, an **802.1Q tag** is inserted into the Ethernet frame.

The VLAN tag contains:

- VLAN ID
- Priority information (802.1p)
- Tag Protocol Identifier (TPID)

This allows the receiving switch to determine which VLAN the frame belongs to.

```text
Ethernet Frame
+--------------------------------+
| 802.1Q VLAN Tag (4 Bytes)      |
+--------------------------------+
```

---

## Native VLAN

The **Native VLAN** is the VLAN whose frames are transmitted **without an 802.1Q tag** on a trunk link.

The default Native VLAN is:

```text
VLAN 1
```

### Configure the Native VLAN

```bash
Switch(config-if)# switchport trunk native vlan 99
```

> **Important:** Both ends of the trunk must use the same Native VLAN to avoid connectivity issues and security risks.

---

# Inter-VLAN Routing Methods

Devices in different VLANs cannot communicate directly because each VLAN is a separate broadcast domain. A Layer 3 device is required to route traffic between VLANs.

---

# 1. Router-on-a-Stick (ROAS)

A **Router-on-a-Stick (ROAS)** uses one physical router interface connected to a switch trunk. Multiple router **subinterfaces** are configured, with one subinterface assigned to each VLAN.

## Topology

<img width="846" height="515" alt="image" src="https://github.com/user-attachments/assets/90074f76-9819-4d50-99a5-42bb5bb78b33" />


### Switch Configuration

```bash
interface GigabitEthernet0/2
 switchport mode trunk
```

### Router Configuration

```bash
interface GigabitEthernet0/1.10
 encapsulation dot1Q 10
 ip address 128.0.0.1 255.255.128.0

interface GigabitEthernet0/1.20
 encapsulation dot1Q 20
 ip address 128.0.128.1 255.255.224.0

interface GigabitEthernet0/1.100
 encapsulation dot1Q 100
 ip address 128.0.0.1 255.255.255.0
```

### Advantages

- Simple configuration
- Low implementation cost
- Requires only one physical router interface

### Disadvantages

- Router becomes a bottleneck.
- Limited scalability.
- Lower performance because all inter-VLAN traffic passes through a single link.

---

# 2. Layer 3 Switch Routing

A Layer 3 switch performs routing internally using **Switch Virtual Interfaces (SVIs)**, eliminating the need for an external router.

### Enable Routing

```bash
Switch(config)# ip routing
```

### Configure SVIs

```bash
interface vlan 10
 ip address 192.168.10.1 255.255.255.0
 no shutdown

interface vlan 20
 ip address 192.168.20.1 255.255.255.0
 no shutdown
```

### Assign Access Ports

```bash
interface GigabitEthernet0/1
 switchport mode access
 switchport access vlan 10
```

### Advantages

- Hardware-based routing
- Very fast performance
- High throughput
- Highly scalable
- Ideal for enterprise networks

### Disadvantages

- Higher cost
- Requires a Layer 3 switch

---

# Comparison of Inter-VLAN Routing Methods

| Feature | Router-on-a-Stick | Layer 3 Switch |
|----------|-------------------|----------------|
| Device Used | Router | Layer 3 Switch |
| Routing Method | Router Subinterfaces | Switch Virtual Interfaces (SVIs) |
| Performance | Moderate | High |
| Scalability | Limited | Excellent |
| Cost | Lower | Higher |
| Best For | Small to Medium Networks | Enterprise Networks |

---

# Common Misconfigurations and Troubleshooting

| Problem | Possible Cause | Solution |
|----------|----------------|----------|
| PC cannot communicate within the same VLAN | Port assigned to the wrong VLAN | Verify using `show vlan brief` and assign the correct VLAN. |
| VLAN not created | VLAN does not exist | Create the VLAN using `vlan <id>`. |
| Trunk not forming | Interface configured as an access port | Configure `switchport mode trunk` on both ends. |
| VLAN missing across trunk | VLAN not allowed on the trunk | Verify using `show interfaces trunk` and allow the VLAN using `switchport trunk allowed vlan`. |
| Native VLAN mismatch | Different Native VLAN IDs on each end | Configure the same Native VLAN on both trunk ports. |
| Router-on-a-Stick not working | Missing or incorrect subinterface configuration | Verify `encapsulation dot1Q <vlan-id>` and IP addressing. |
| Layer 3 switch routing not working | `ip routing` disabled or SVI down | Enable `ip routing` and ensure the SVI is up with at least one active access port in the VLAN. |
| Hosts cannot reach the gateway | Incorrect default gateway | Configure the default gateway to match the router subinterface or SVI IP address. |
| VLAN traffic blocked | Incorrect ACL or security policy | Review ACLs and routing policies. |
| Connectivity failure | Interface or cabling issue | Verify interfaces using `show interfaces status` and `show ip interface brief`. |

---

# Useful Troubleshooting Commands

```bash
show vlan brief
show interfaces trunk
show interfaces switchport
show mac address-table
show running-config
show ip interface brief
show ip route
show interfaces status
show cdp neighbors
ping
traceroute
```

These commands help verify:

- VLAN assignments
- Trunk status
- Interface configurations
- MAC address learning
- Routing tables
- Physical connectivity
- End-to-end communication

---


[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br> [← Previous](../01-network-design-fundamentals/README.md) · [Next →](../03-switching-technologies/README.md)
