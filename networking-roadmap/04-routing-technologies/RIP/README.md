
[⬅ Routing Technologies (Static, RIP, OSPF)](../../04-routing-technologies/README.md) <br><br> [Static Routing](../../04-routing-technologies/static-routing/README.md) · [OSPF](../../04-routing-technologies/OSPF/README.md)

---

# Routing Information Protocol (RIP)

The **Routing Information Protocol (RIP)** is one of the oldest and simplest **dynamic routing protocols**. It is classified as a **distance-vector routing protocol**, meaning that routers exchange routing information with directly connected neighbors and determine the best path based on **distance (hop count)** and **direction (next-hop router)**.

Unlike **static routing**, where routes are manually configured, RIP automatically learns, advertises, and updates routes whenever the network topology changes.

RIP is best suited for **small networks** due to its limited scalability and slow convergence.

---

# Distance-Vector Protocol Basics

A **distance-vector routing protocol** determines the best path based on two pieces of information:

- **Distance** – The metric used to reach the destination.
- **Vector** – The direction or next-hop router used to reach the destination.

In RIP:

- Distance = **Hop Count**
- Vector = **Next-Hop Router**

Every **30 seconds**, each RIP router sends its **entire routing table** to all directly connected RIP neighbors.

This periodic update allows routers to learn new routes and detect topology changes.

Example:

```text
        R2
       /  \
      /    \
    R1 ---- R3
```

Suppose R1 knows about network **192.168.3.0/24** through R2.

R2 advertises:

```text
Network: 192.168.3.0/24
Metric: 1 hop
```

R1 adds one hop before storing the route.

```text
192.168.3.0/24

Metric = 2 hops
```

---

# How RIP Works

RIP follows these basic steps:

1. Routers discover directly connected networks.
2. Routers periodically advertise their routing tables.
3. Neighboring routers receive the updates.
4. Each router increments the hop count by one.
5. The router installs the route with the **lowest hop count**.
6. Routing tables are updated automatically whenever the topology changes.

---

# Hop Count Metric

RIP measures the best route using **hop count**.

A **hop** is the number of routers a packet must pass through to reach the destination.

Example:

```text
R1 ----- R2 ----- R3 ----- R4

R1 to R4 = 3 hops
```

Each router crossed increases the metric by **1**.

---

## Maximum Hop Count

| Hop Count | Meaning |
|-----------|---------|
| 0–15 | Reachable |
| 16 | Unreachable (Infinity) |

Because RIP considers **16 hops** as unreachable, it is only practical for **small networks**.

---

# RIP Timers

RIP uses several timers to maintain routing information.

| Timer | Default Value | Purpose |
|--------|--------------:|----------|
| Update Timer | 30 seconds | Sends routing updates to neighbors. |
| Invalid Timer | 180 seconds | Marks a route as invalid if no updates are received. |
| Hold-down Timer | 180 seconds | Prevents unstable routing updates during convergence. |
| Flush Timer | 240 seconds | Removes invalid routes from the routing table. |

These timers help prevent routing loops and maintain accurate routing information.

---

# RIP Version 1 vs RIP Version 2

There are two versions of RIP.

## RIP Version 1 (RIPv1)

RIPv1 is a **classful routing protocol**.

Characteristics:

- Does not support VLSM.
- Does not support CIDR.
- Automatically summarizes routes.
- Uses broadcast updates.
- Does not support authentication.

---

## RIP Version 2 (RIPv2)

RIPv2 is a **classless routing protocol** and is the version commonly used today.

Characteristics:

- Supports VLSM.
- Supports CIDR.
- Supports route authentication.
- Uses multicast updates (224.0.0.9).
- Supports disabling automatic summarization.

---

## RIP v1 vs RIP v2 Comparison

| Feature | RIP v1 | RIP v2 |
|----------|---------|---------|
| Classless Routing | No | Yes |
| VLSM Support | No | Yes |
| CIDR Support | No | Yes |
| Authentication | No | Yes |
| Route Summarization | Automatic Only | Automatic (can be disabled) |
| Update Method | Broadcast | Multicast (224.0.0.9) |

> **Note:** In modern networks, **RIPv2** is almost always used because it supports Variable Length Subnet Masking (VLSM), Classless Inter-Domain Routing (CIDR), and route authentication.

---

# Basic RIP Version 2 Configuration

Enable RIP:

```bash
Router(config)# router rip
```

Specify RIP Version 2:

```bash
Router(config-router)# version 2
```

Disable automatic summarization:

```bash
Router(config-router)# no auto-summary
```

Advertise connected networks:

```bash
Router(config-router)# network 192.168.1.0
Router(config-router)# network 10.0.0.0
```

Exit configuration mode:

```bash
Router(config-router)# end
```

---

# Example Configuration

Suppose Router R1 has two directly connected networks:

```text
192.168.12.0/26
192.168.12.128/30
```
Router R2 has two directly connected networks:

```text
192.168.12.64/26
192.168.12.128/30
```

<img width="824" height="355" alt="image" src="https://github.com/user-attachments/assets/e425958a-1839-4d7a-b48b-db914a783ba8" />

Configuration:
R1
```bash
R1(config)# router rip
R1(config-router)# version 2
R1(config-router)# no auto-summary
R1(config-router)# network 192.168.12.0
R1(config-router)# network 192.168.12.128
```
R2
```bash
R2(config)# router rip
R2(config-router)# version 2
R2(config-router)# no auto-summary
R2(config-router)# network 192.168.12.64
R2(config-router)# network 192.168.12.128
```
<img width="1920" height="764" alt="image" src="https://github.com/user-attachments/assets/de447828-5260-4731-ab9d-291c8d9aeb85" />

#### Verify Connectivity
Ping from PC-A to PC-B and vice versa.
<img width="1919" height="692" alt="image" src="https://github.com/user-attachments/assets/d96a786b-5cee-4d5b-ab11-5d85b7bbadf7" />


---

# Advantages of RIP

- Easy to configure.
- Easy to understand.
- Automatic route learning.
- Low CPU and memory requirements.
- Suitable for small networks.
- Vendor-independent (RIPv2).

---

# Limitations of RIP

- Maximum hop count of 15.
- Slow convergence.
- Periodically sends the entire routing table every 30 seconds.
- Consumes bandwidth due to frequent routing updates.
- Not suitable for large enterprise networks.
- May converge more slowly after topology changes compared to link-state protocols.

---

# RIP Route Codes

When viewing the routing table:

```bash
show ip route
```

RIP routes appear with the code:

```text
R
```

Example:

```text
R    192.168.2.0/24
```

---

# RIP Verification Commands

Display the routing table:

```bash
show ip route
```

Display RIP configuration:

```bash
show ip protocols
```

Display the RIP database:

```bash
show ip rip database
```

Display RIP neighbors (if supported):

```bash
show ip rip database
```

Monitor RIP updates:

```bash
debug ip rip
```

Stop debugging:

```bash
undebug all
```

---

# Common RIP Problems and Troubleshooting

| Problem | Possible Cause | Solution |
|----------|----------------|----------|
| RIP routes not appearing | Incorrect `network` statement | Verify advertised networks using `show ip protocols`. |
| No neighbor updates | RIP not enabled or interfaces not participating | Verify RIP configuration and interface IP addresses. |
| Version mismatch | One router uses RIP v1 while another uses RIP v2 | Configure all routers to use the same RIP version. |
| Routes incorrectly summarized | Automatic summarization enabled | Disable summarization using `no auto-summary`. |
| Missing routes | Network statement does not match the interface network | Verify interface addressing and network statements. |
| Cannot reach remote network | Layer 1, Layer 2, or Layer 3 issue | Verify interface status, IP addressing, and routing table. |

---

# Best Practices for RIP

- Use **RIPv2** instead of RIPv1.
- Disable automatic summarization unless specifically required.
- Use RIP only in **small networks**.
- Verify routing updates using `show ip protocols`.
- Monitor routing changes using `debug ip rip` during troubleshooting.
- Consider using **OSPF** or **EIGRP** for medium to large enterprise networks.

---

# Summary

The **Routing Information Protocol (RIP)** is a simple **distance-vector routing protocol** that automatically exchanges routing information with neighboring routers. It selects the best path using **hop count** as its routing metric and periodically advertises its routing table every **30 seconds**. While RIP is easy to configure and suitable for small networks, its **15-hop limitation**, **slow convergence**, and **periodic routing updates** make it unsuitable for larger or more complex networks. In modern enterprise environments, **RIPv2** is preferred over RIPv1 because it supports **VLSM**, **CIDR**, **authentication**, and **multicast updates**.

---

[Static Routing](../../04-routing-technologies/static-routing/README.md) · [OSPF](../../04-routing-technologies/OSPF/README.md) <br><br> 
[⬅ Routing Technologies (Static, RIP, OSPF)](../../04-routing-technologies/README.md) 
