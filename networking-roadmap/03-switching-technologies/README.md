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

<img width="831" height="693" alt="image" src="https://github.com/user-attachments/assets/d8554a72-fce4-4e7f-8d34-a1fa6f97d5b1" />


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

---

# EtherChannel

**EtherChannel** combines multiple physical Ethernet links into a **single logical interface (Port-Channel)**.

Instead of forwarding traffic over one cable, EtherChannel distributes traffic across multiple links while appearing as a single connection.

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

# 1. Static EtherChannel

No negotiation occurs.

Both switches must be manually configured.

```bash
interface range GigabitEthernet0/1-2
 channel-group 1 mode on

interface Port-channel1
 switchport mode trunk
```

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

```bash
interface range GigabitEthernet0/1-2
 channel-group 1 mode desirable
```

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

```bash
interface range GigabitEthernet0/1-2
 channel-group 1 mode active
```

---

# Verify EtherChannel

```bash
show etherchannel summary
```

Example output:

```text
Group  Port-channel  Protocol
1      Po1(SU)       LACP
```

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

Configure load balancing:

```bash
Switch(config)# port-channel load-balance src-dst-ip
```

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
