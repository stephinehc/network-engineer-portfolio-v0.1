# 3. Switching Technologies (STP, EtherChannel)

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br> [← Previous](../02-lan-technologies/README.md) · [Next →](../04-routing-technologies/README.md)

---

## Overview
<p align="justify">
This section explores the switching technologies that ensure redundancy, loop prevention, and efficient use of bandwidth in enterprise LANs. Spanning Tree Protocol (STP) prevents broadcast storms caused by loops, while EtherChannel aggregates multiple physical links into one logical channel for higher throughput and fault tolerance.
</p>

---

## Topics Covered
- Spanning Tree Protocol (STP)
  - Purpose and operation
  - Root bridge election
  - Port states (Blocking, Listening, Learning, Forwarding)
  - Rapid Spanning Tree Protocol (RSTP) enhancements
- EtherChannel
  - Benefits of link aggregation
  - Configuration methods (PAgP, LACP, static)
  - Load balancing options
  - Troubleshooting EtherChannel misconfigurations

---

## Notes & Diagrams
- **STP Key Point**: Prevents loops by blocking redundant paths while keeping one active path to the root bridge.
- **RSTP Advantage**: Faster convergence compared to traditional STP.
- **EtherChannel Example**:  
  - Four FastEthernet links bundled into one logical channel → higher bandwidth and redundancy.

# Spanning Tree Protocol (STP)

The **Spanning Tree Protocol (STP)** is a Layer 2 network protocol defined by **IEEE 802.1D** that prevents switching loops in networks with redundant links.

Without STP, redundant links can create **broadcast storms**, **multiple frame copies**, and **MAC address table instability**, causing the network to become unusable.

STP automatically detects redundant paths and places certain switch ports into a blocking state while keeping backup links available in case the active path fails.

> **Note:** Cisco switches have **Spanning Tree Protocol (STP) enabled by default** on all VLANs to prevent Layer 2 switching loops. Depending on the Cisco IOS version and switch model, the default implementation is either **Per-VLAN Spanning Tree Plus (PVST+)** or **Rapid Per-VLAN Spanning Tree Plus (Rapid PVST+)**. Because STP is enabled by default, network administrators typically do **not** need to manually enable it, but they often configure the **Root Bridge**, **PortFast**, **BPDU Guard**, and other STP features to optimize network performance and security.

---

# Purpose and Operation

The primary purpose of STP is to create a **loop-free logical topology** while maintaining physical redundancy.

STP operates through the following steps:

1. Elects a **Root Bridge**.
2. Determines the shortest path to the Root Bridge.
3. Selects Root Ports and Designated Ports.
4. Places redundant ports into the **Blocking** state.
5. Continuously monitors the network for topology changes.

### Example

<img width="1059" height="689" alt="image" src="https://github.com/user-attachments/assets/bf2e5736-d660-40ce-9811-02e4ebd24060" />



Without STP, frames may loop indefinitely.

With STP, one redundant link is blocked.

If an active link fails, STP automatically activates the blocked link.

> **Note:** Since STP is enabled by default on Cisco switches, connecting redundant links between switches without any additional configuration will **not** immediately create a switching loop. STP automatically blocks one or more redundant paths to maintain a loop-free topology. If STP is manually disabled, redundant links can cause **broadcast storms**, **MAC address table instability**, and **multiple frame copies**, potentially bringing down the entire Layer 2 network. Interface g0/1 on S2 is currently in BLOCK state.

---

# Root Bridge Election

The **Root Bridge** is the central reference point for all STP calculations.

Each switch advertises a **Bridge ID (BID)** consisting of:

- Bridge Priority
- MAC Address

```text
Bridge ID = Bridge Priority + MAC Address
```

The switch with the **lowest Bridge ID** becomes the Root Bridge.

### Election Rules

1. Lowest Bridge Priority wins.
2. If priorities are equal, the lowest MAC address wins.

Default bridge priority:

```text
32768
```
```text
Example 1:
S1
Bridge Priority: 32868
MAC Address of the switch: 0060.FFBC.AAB5
Bridge ID: 32868.00D0.FFBC.AAB5
S2
Bridge Priority: 32788
MAC Address of the switch: 00D0.FFBC.AAB5
Bridge ID: 32788.0060.FFBC.AAB5

S2 becomes the root bridge because it has a lower bridge priority.

Example 2 (If the same bridge priority):
S1
Bridge Priority: 32868
MAC Address of the switch: 0060.FFBC.AAB5
Bridge ID: 32868.00D0.FFBC.AAB5
S2
Bridge Priority: 32868
MAC Address of the switch: 00D0.FFBC.AAB5
Bridge ID: 32868.0060.FFBC.AAB5

S1 becomes the root bridge since it has the lowest value in terms of MAC address.
```
### Indentifying the Root Bridge
Enter the command on each switch.
```bash
Switch# show spanning-tree
```
Check the information under the Root ID and locate the MAC address on each of the following switches.

Base on the information of the three switches below:

Root ID for both VLAN 10 and VLAN 20 is 32788.0060.3EC4.7B9D

S1 Bridge ID for both VLAN 10 and VLAN 20 is 32788.00D0.BCCD.00E0

<img width="666" height="654" alt="image" src="https://github.com/user-attachments/assets/52689464-fe7d-4fbf-8651-ff9b963f15b4" />

S2 Bridge ID for both VLAN 10 and VLAN 20 is 32788.00D0.FFBC.AAB5

<img width="690" height="686" alt="image" src="https://github.com/user-attachments/assets/53104ed8-3e8d-40f6-ae40-4cf95ce768e1" />

S3 Bridge ID for both VLAN 10 and VLAN 20 is 32788.0060.3EC4.7B9D

<img width="665" height="605" alt="image" src="https://github.com/user-attachments/assets/a5173acb-a2c0-420c-8ffa-fb92333fc641" />

**Therefore the Root Bridge is S3 base on the information of the ROOT ID.**

### Configure a switch as the Root Bridge:

```bash
Switch(config)# spanning-tree vlan 10 priority 4096
```

Or use Cisco's shortcut:

```bash
Switch(config)# spanning-tree vlan 10 root primary
```

Verify:

```bash
Switch# show spanning-tree
```

---

# Port Roles

STP assigns different roles to switch ports.

## Root Port (RP)

- One per non-root switch
- Has the lowest cost path toward the Root Bridge
- Always in the Forwarding state

---

## Designated Port (DP)

- One per network segment
- Forwards traffic away from the Root Bridge
- Always in the Forwarding state

---

## Non-Designated Port (Blocked Port)

- Prevents switching loops
- Does not forward user traffic
- Acts as a backup path

---

# STP Port States

A switch port transitions through several states before forwarding traffic.

## 1. Blocking

- Does not forward frames
- Does not learn MAC addresses
- Receives only BPDUs
- Prevents loops

---

## 2. Listening

- Processes BPDUs
- Determines network topology
- Does not forward frames
- Does not learn MAC addresses

Default duration:

```text
15 seconds
```

---

## 3. Learning

- Learns MAC addresses
- Does not forward user traffic
- Builds the MAC address table

Default duration:

```text
15 seconds
```

---

## 4. Forwarding

- Forwards user traffic
- Learns MAC addresses
- Normal operating state

---

## 5. Disabled

- Interface is administratively down or has failed.
- Does not participate in STP.

---

# STP Port State Transition

```text
Blocking
    │
    ▼
Listening
    │
    ▼
Learning
    │
    ▼
Forwarding
```

Total convergence time (default):

```text
20 + 15 + 15 = 50 seconds
```

---

# Rapid Spanning Tree Protocol (RSTP)

**Rapid Spanning Tree Protocol (RSTP)** is defined by **IEEE 802.1w** and is an enhancement over traditional STP.

RSTP significantly reduces convergence time from approximately **50 seconds** to **1–6 seconds**.

---

## RSTP Enhancements

- Much faster convergence
- New port roles (Alternate and Backup)
- Immediate transition for edge ports
- Faster recovery after link failures
- Backward compatible with STP

---

## RSTP Port States

RSTP simplifies the original STP states into three states.

| STP | RSTP |
|------|------|
| Blocking | Discarding |
| Listening | Discarding |
| Learning | Learning |
| Forwarding | Forwarding |

---

## Enable Rapid PVST+

```bash
Switch(config)# spanning-tree mode rapid-pvst
```

Verify:

```bash
Switch# show spanning-tree summary
```
<img width="607" height="345" alt="image" src="https://github.com/user-attachments/assets/17bbe940-ddea-4718-9d05-c5f134db1bd9" />

---

# EtherChannel

**EtherChannel** combines multiple physical Ethernet links into a **single logical interface (Port-Channel)**.

Instead of forwarding traffic over one cable, EtherChannel distributes traffic across multiple links while appearing as a single connection.

> NOTE: When combining multiple physical Ethernet links, the links should have the same parameters such as the speed, duplex, vlan association (trunk or access mode), and same allowed vlans (if in trunking).

---

# Benefits of Link Aggregation

## Increased Bandwidth

Combines multiple links into one logical connection.

Example:

```text
4 × 1 Gbps = 4 Gbps logical bandwidth
```

---

## Redundancy

If one physical link fails, traffic automatically continues through the remaining links.

---

## Load Balancing

Traffic is distributed across multiple links, preventing congestion.

---

## Simplified STP

STP treats the Port-Channel as **one logical interface** instead of multiple separate links.

This eliminates unnecessary blocked ports.

---

# EtherChannel Configuration Methods

Cisco supports three methods for creating EtherChannels.

| Protocol | Standard | Negotiation |
|----------|----------|-------------|
| Static | No | None |
| PAgP | Cisco Proprietary | Yes |
| LACP | IEEE 802.3ad / IEEE 802.1AX | Yes |

---

Let us consider this given topology below to demonstrate the 3 methods of etherchannel configuration. <br>
S1 to S2 (Static EtherChannel) <br>
S2 to S3 (PAgP EtherChannel) <br>
S3 to S1 (LACP EtherChannel) <br>
<img width="835" height="614" alt="image" src="https://github.com/user-attachments/assets/0a1c65c7-f4d5-4b84-8721-3040ef4284cc" />

> In the given design above, the links does not have the same speed (GigabitEthernet and FastEthernet). If you try to bundle links of different speeds (say, one 1 Gbps and one 100 Mbps):
  - The EtherChannel will fail to form (most Cisco switches will put the mismatched port into suspended or err‑disabled state).
  - Even if it somehow comes up, load‑balancing will be uneven — traffic could be hashed onto the slower link, creating bottlenecks and packet drops

For best practice, we will match the links. <br>

<img width="892" height="659" alt="image" src="https://github.com/user-attachments/assets/7d280da5-4d92-475f-9561-151827c4e103" />


# 1. Static EtherChannel

Refer to the figure above, link for S1 to S2.

No negotiation occurs.

Both switches must be manually configured.

```text
interface range f0/1,f0/20
 switchport mode trunk
 switchport trunk native vlan 100 (In our case we moved the Native VLAN to VLAN 100. If you did not change your native VLAN, you dont need to enter this command)
 channel-group 1 mode on

interface Port-channel1
 switchport mode trunk
 switchport trunk native vlan 100
```
If you encountered this error below upon configuring the interface as trunk: 
```bash
Command rejected: An interface whose trunk encapsulation is "Auto" can not be configured to "trunk" mode.
```
Enter this command:
```bash
S1(config-if)# switchport trunk encapsulation dot1q
```
Verify:
Port-channel group 1 (bundle of f0/1 and f0/20 on S1) is now active (SU). <br>

<img width="647" height="484" alt="image" src="https://github.com/user-attachments/assets/e3a96bac-5929-4753-ad31-16435963323b" />

Port-channel group 1 (bundle of f0/1 and f0/20 on S2) is now active (SU). <br>

<img width="602" height="489" alt="image" src="https://github.com/user-attachments/assets/69ef28cb-26eb-448c-8154-5c00bb122fd8" />


---

# 2. PAgP (Port Aggregation Protocol)

Cisco proprietary protocol.

### Modes

| Mode | Description |
|------|-------------|
| desirable | Actively negotiates |
| auto | Passively waits |

Successful combinations:

- desirable ↔ desirable
- desirable ↔ auto

Example:

Refer to the figure above. Connection between S2 and S3.
```bash
interface range f0/3,f0/21
 switchport mode trunk
 switchport trunk native vlan 100
 channel-group 2 mode desirable

interface Port-channel2
 switchport mode trunk
 switchport trunk native vlan 100
```
Port-channel will not be activated (SU) yet unless the other end of the switch has been configured. As we can see below, Po2 shows SD. <br>

<img width="623" height="488" alt="image" src="https://github.com/user-attachments/assets/069561a1-b23c-481a-9949-bad4f4b8aba6" />

Configure S3 and wait for this notification in the CLI to show: 
```bash
%LINK-5-CHANGED: Interface Port-channel2, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface Port-channel2, changed state to up
```
Then we verify it on S3 and S2. <br>

<img width="603" height="473" alt="image" src="https://github.com/user-attachments/assets/c19a4a38-1cca-4a46-9523-d8bf11e5e82a" />

<img width="584" height="556" alt="image" src="https://github.com/user-attachments/assets/3f006691-de1b-41dd-8990-41f0b5aab43a" />

We can now see that Po2 is now successfully established using PAgP.

---

# 3. LACP (Link Aggregation Control Protocol)

IEEE standard protocol supported by multiple vendors.

### Modes

| Mode | Description |
|------|-------------|
| active | Actively negotiates |
| passive | Waits for negotiation |

Successful combinations:

- active ↔ active
- active ↔ passive

Example:

Refer to the figure above. Connection between S3 and S1.
```bash
interface range f0/2,f0/22
 switchport mode trunk
 switchport trunk native vlan 100
 channel-group 3 mode active

interface Port-channel3
 switchport mode trunk
 switchport trunk native vlan 100
```
Upon combining interfaces f0/2 and f0/22, we have identified that it cant be combined succesfully.
```text
%EC-5-CANNOT_BUNDLE2: Fa0/2 is not compatible with Po3 and will be suspended  (native vlan of Fa0/2 is 100, Po3 id 1)
%EC-5-CANNOT_BUNDLE2: Fa0/22 is not compatible with Po3 and will be suspended  (native vlan of Fa0/22 is 100, Po3 id 1)
```
Upon troubleshooting, f0/22 belonged to vlan 10 which is in access mode (see the figure below). We need to remove the access mode of f0/22. 

<img width="662" height="316" alt="image" src="https://github.com/user-attachments/assets/0f42c690-2760-4253-8724-82ed20f52c1a" />

```bash
S1(config)#interface f0/22
S1(config-if)#no switchport access vlan 10
S1(config)#int po3
S1(config-if)#no shutdown
```

Verify if Po3 is created on S1 already. <br>

<img width="570" height="295" alt="image" src="https://github.com/user-attachments/assets/c783b750-1688-455f-85dd-cd007fbea7cd" />

We can now see that Po3 is successfully created on S1 but is not yet active (SD). Configure S3 to fully establish the link aggregation.
```bash
interface range f0/2,f0/22
 switchport mode trunk
 switchport trunk native vlan 100
 channel-group 3 mode passive

interface Port-channel3
 switchport mode trunk
 switchport trunk native vlan 100
```
<img width="574" height="290" alt="image" src="https://github.com/user-attachments/assets/be0a6af6-52a9-4ed7-bb15-e8dfb3523758" />

Port-channel 3 is now successfully established using LACP.


---

# Verify EtherChannel

```bash
show etherchannel summary
```
> NOTE: Spanning-tree protocol will take a couple of minutes to take effect.

<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/9b49483c-27e0-43f7-ace2-e14aa44684cc" />


---

# Load Balancing Options

EtherChannel does **not** send one packet on one cable and the next packet on another.

Instead, it uses a **hashing algorithm** to determine which physical link carries the traffic.

Common load-balancing methods include:

- Source MAC address
- Destination MAC address
- Source IP address
- Destination IP address
- Source and Destination IP address
- Source and Destination TCP/UDP port numbers

View the current load-balancing method:

```bash
Switch# show etherchannel load-balance
```
<img width="478" height="133" alt="image" src="https://github.com/user-attachments/assets/00efb270-f984-44dd-8b8c-3eaee73db9d7" />

Configure load balancing:

```bash
Switch(config)# port-channel load-balance src-dst-ip
```
<img width="536" height="200" alt="image" src="https://github.com/user-attachments/assets/08154350-8569-47bc-82bc-d8c1862fcc55" />

---

# Troubleshooting EtherChannel Misconfigurations

| Problem | Possible Cause | Solution |
|----------|----------------|----------|
| EtherChannel not forming | Different protocols (LACP vs PAgP) | Configure the same protocol on both switches. |
| Ports suspended | VLAN or trunk mismatch | Verify VLANs and trunk configuration. |
| Port-channel down | Speed or duplex mismatch | Configure identical speed and duplex settings. |
| Interfaces not bundled | Different interface configurations | Ensure all member ports have identical settings. |
| One side uses `mode on`, the other uses LACP/PAgP | Negotiation mismatch | Configure matching channel-group modes. |
| STP blocking unexpected links | Port-channel not formed correctly | Verify the Port-Channel interface and member ports. |
| Native VLAN mismatch | Different native VLANs | Configure the same native VLAN on both ends. |

---

# Useful Troubleshooting Commands

```bash
show etherchannel summary
show etherchannel port-channel
show interfaces port-channel
show interfaces trunk
show spanning-tree
show running-config
show lacp neighbor
show pagp neighbor
```

These commands help verify:

- EtherChannel status
- Member interfaces
- Negotiation protocol
- Trunk configuration
- STP status
- LACP and PAgP neighbors
- Overall Port-Channel health

---

## Resources
- 📖 [Cisco Networking Academy: CCNA: Switching, Routing, and Wireless Essentials](https://www.netacad.com/courses/ccna-switching-routing-wireless-essentials?courseLang=en-US)
- 🧩 Cisco Packet Tracer

---

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br> [← Previous](../02-lan-technologies/README.md) · [Next →](../04-routing-technologies/README.md)
