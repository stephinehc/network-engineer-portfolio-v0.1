[⬅ Routing Technologies (Static, RIP, OSPF)](../../04-routing-technologies/README.md) <br><br> [Static Routing](../../04-routing-technologies/static-routing/README.md) · [RIP](../../04-routing-technologies/RIP/README.md)

---

# Open Shortest Path First (OSPF)

**Open Shortest Path First (OSPF)** is a **dynamic link-state routing protocol** standardized by the **Internet Engineering Task Force (IETF)**. It is one of the most widely used **Interior Gateway Protocols (IGPs)** in enterprise networks because of its **fast convergence**, **efficient routing**, and **excellent scalability**.

Unlike **RIP**, which exchanges its entire routing table every 30 seconds, OSPF exchanges routing information only when network changes occur. This significantly reduces bandwidth consumption and allows OSPF to converge much faster.

OSPF uses the **Shortest Path First (SPF)** algorithm, also known as the **Dijkstra Algorithm**, to calculate the best path to every destination network.

> NOTE: In this portion, we will focus only in Single-area OSPF.

---

# Link-State Protocol Fundamentals

A **link-state routing protocol** allows each router to build a complete map of the network topology.

Instead of periodically sending its entire routing table, OSPF performs the following steps:

1. Discovers neighboring OSPF routers.
2. Forms neighbor adjacencies.
3. Exchanges Link-State Advertisements (LSAs).
4. Builds a Link-State Database (LSDB).
5. Uses the SPF (Dijkstra) algorithm to calculate the shortest path.
6. Installs the best routes into the routing table.

Unlike RIP, every OSPF router has a complete view of the network topology.

---

# OSPF Neighbor Formation

Before routers exchange routing information, they must first become neighbors.

The process includes:

1. Sending Hello Packets.
2. Discovering neighboring routers.
3. Verifying OSPF parameters match.
4. Establishing adjacency.
5. Exchanging Link-State Advertisements (LSAs).

Some OSPF parameters that must match include:

- Area ID
- Hello Timer
- Dead Timer
- Authentication
- Stub Area configuration
- IP subnet

If any of these parameters do not match, routers will not become OSPF neighbors.

---

# Router ID (RID)

Every OSPF router requires a unique **Router ID (RID)**.

The Router ID uniquely identifies the router within the OSPF domain.

Selection priority:

1. Manually configured Router ID
2. Highest Loopback Interface IP Address
3. Highest Active Physical Interface IP Address

Configure a Router ID:

```bash
Router(config)# router ospf 1
Router(config-router)# router-id 1.1.1.1
```

Verify:

```bash
show ip ospf
```

> **Note:** If the Router ID is changed after OSPF has started, the OSPF process must be restarted for the new Router ID to take effect.

---

# Cost Metric Calculation

Unlike RIP, OSPF does **not** use hop count.

Instead, it uses **Cost**.

The Cisco default cost formula is:

```text
Cost = Reference Bandwidth / Interface Bandwidth
```

Default reference bandwidth:

```text
100 Mbps
```

Examples:

| Interface Speed | Cost |
|-----------------|-----:|
| 10 Mbps | 10 |
| 100 Mbps | 1 |
| 1 Gbps | 1* |
| 10 Gbps | 1* |

> **Note:** Because the default reference bandwidth is only **100 Mbps**, interfaces faster than 100 Mbps all receive a cost of **1**. To accurately distinguish Gigabit and faster interfaces, the reference bandwidth should be increased.

Example:

```bash
Router(config-router)# auto-cost reference-bandwidth 10000
```

This sets the reference bandwidth to **10 Gbps**.

---

# OSPF Areas and Hierarchical Design

One of OSPF's major advantages is its ability to divide large networks into **areas** which is called **multi-area OSPF**.

An **Area** is a logical grouping of routers that share the same Link-State Database (LSDB).

Benefits include:

- Smaller routing tables
- Reduced CPU utilization
- Less LSA flooding
- Faster convergence
- Better scalability

---

## Backbone Area (Area 0)

Area 0 is the **backbone** of every OSPF network.

All other areas must connect to Area 0.

Example:

```text
             Area 1
                |
                |
Area 0 -------- ABR -------- Area 2
                |
             Area 3
```

---

## Area Border Router (ABR)

An **Area Border Router (ABR)** connects multiple OSPF areas.

Responsibilities:

- Connects Area 0 to other areas.
- Maintains separate LSDBs for each area.
- Summarizes routes between areas.

---

## Autonomous System Boundary Router (ASBR)

An **Autonomous System Boundary Router (ASBR)** redistributes routes from another routing protocol into OSPF.

Example:

```text
Static Routes
       |
      ASBR
       |
     OSPF Domain
```

---

# Designated Router (DR) and Backup Designated Router (BDR)

On multi-access networks (such as Ethernet), OSPF elects:

- Designated Router (DR)
- Backup Designated Router (BDR)

Purpose:

- Reduce the number of OSPF adjacencies.
- Reduce Link-State Advertisement (LSA) flooding.

Instead of every router forming an adjacency with every other router, routers communicate through the DR.

---

## DR/BDR Election

Election priority:

1. Highest Interface Priority
2. Highest Router ID

Default OSPF interface priority:

```text
1
```

Disable participation:

```bash
interface GigabitEthernet0/0
 ip ospf priority 0
```

Verify:

```bash
show ip ospf neighbor
```

Example:

```text
Neighbor ID     State
2.2.2.2         FULL/DR
3.3.3.3         FULL/BDR
```

---

# OSPF Packet Types

OSPF uses five packet types.

| Packet | Purpose |
|---------|----------|
| Hello | Discovers and maintains neighbors |
| Database Description (DBD) | Summarizes the Link-State Database |
| Link-State Request (LSR) | Requests additional LSAs |
| Link-State Update (LSU) | Sends requested LSAs |
| Link-State Acknowledgment (LSAck) | Acknowledges receipt of LSAs |

---

# Basic Single-Area OSPF Configuration
Configuration:
```bash
Router(config)# router ospf <value>
Router(config-router)# network 192.168.1.0 <wildcard mask> area 0
Router(config-router)# network 192.168.2.0 <wildcard mask> area 0
```
# OSPF Wildcard Masks

Unlike subnet masks, OSPF uses **Wildcard Masks**.

A wildcard mask is the inverse of a subnet mask.

  **255.255.255.255 - subnet mask = wildcard mask**
  
Examples:

| Subnet Mask | Wildcard Mask |
|--------------|---------------|
| 255.255.255.0 | 0.0.0.255 |
| 255.255.255.252 | 0.0.0.3 |
| 255.255.0.0 | 0.0.255.255 |

Example:

```bash
network 192.168.10.0 0.0.0.255 area 0
```

---

# OSPF Route Code

View the routing table:

```bash
show ip route
```

OSPF routes appear with:

```text
O
```

Example:

```text
O    192.168.2.0/24
```



---

# Example Scenario:
<img width="884" height="385" alt="image" src="https://github.com/user-attachments/assets/ff36eca1-090a-499b-b4fd-01751bda5a79" />


Enable OSPF Process and advertise directly connected networks:

For R1
```bash
R1(config)# router ospf 1
R1(config-router)# network 192.168.12.128 0.0.0.3 area 0
R1(config-router)# network 192.168.12.0 0.0.0.63 area 0
```
For R2
```bash
R2(config)# router ospf 1
R2(config-router)# network 192.168.12.128 0.0.0.3 area 0
R2(config-router)# network 192.168.12.64 0.0.0.63 area 
```
<img width="1920" height="830" alt="image" src="https://github.com/user-attachments/assets/f9c0f608-8211-4d96-9b5c-7ae753039815" />

Exit configuration mode:

```bash
Router(config-router)# end
```
Verify OSPF route:

On R1, it shows the route of the network 192.168.12.64/26 and is using OSPF (O).
<img width="686" height="303" alt="image" src="https://github.com/user-attachments/assets/e5765739-3fa7-434e-add8-c229cc698d47" />

On R2, it shows the route of the network 192.168.12.0/26 and is using OSPF (O).
<img width="724" height="268" alt="image" src="https://github.com/user-attachments/assets/fd46d48d-09e7-4971-a2b9-c44c7c9e90d8" />

Verify connectivity:

Ping and trace route from PC-A to PC-b and vice versa.
<img width="1895" height="742" alt="image" src="https://github.com/user-attachments/assets/096fa0fd-6624-405d-91dd-04cd054ef2f1" />

---

# Advantages of OSPF

- Fast convergence.
- Highly scalable.
- Supports hierarchical network design.
- Supports VLSM and CIDR.
- Efficient bandwidth utilization.
- Vendor-independent (open standard).
- Supports authentication.
- Calculates optimal paths using the SPF algorithm.

---

# Limitations of OSPF

- More complex to configure than RIP.
- Higher CPU and memory requirements.
- Requires careful area planning in large networks.
- Initial database synchronization can take time.

---

# OSPF Verification Commands

Display routing table:

```bash
show ip route
```

Display OSPF neighbor table:

```bash
show ip ospf neighbor
```

Display OSPF interfaces:

```bash
show ip ospf interface
```

Display OSPF database:

```bash
show ip ospf database
```

Display OSPF configuration:

```bash
show ip protocols
```

Display Router ID:

```bash
show ip ospf
```

Monitor OSPF events:

```bash
debug ip ospf events
```

Stop debugging:

```bash
undebug all
```

---

# Common OSPF Problems and Troubleshooting

| Problem | Possible Cause | Solution |
|----------|----------------|----------|
| OSPF neighbors not forming | Area mismatch | Verify Area IDs on both routers. |
| Neighbor stuck in INIT state | One-way communication | Verify interface connectivity and Hello packets. |
| Neighbor stuck in EXSTART/EXCHANGE | MTU mismatch | Configure matching MTU values or use `ip ospf mtu-ignore`. |
| Missing routes | Incorrect network statement | Verify advertised networks using `show ip protocols`. |
| Incorrect path selection | Incorrect interface cost | Verify interface cost using `show ip ospf interface`. |
| Router not participating in OSPF | Interface not included in an OSPF network statement | Verify OSPF configuration. |
| Frequent neighbor loss | Hello/Dead timer mismatch | Verify timer values on both routers. |
| Duplicate Router IDs | Router ID conflict | Configure a unique Router ID and restart the OSPF process. |

---

# Best Practices for OSPF

- Assign a unique **Router ID** to every router.
- Use **Area 0** as the backbone area.
- Design hierarchical networks using multiple areas for large deployments.
- Increase the **reference bandwidth** to accurately calculate costs on Gigabit and faster links.
- Verify neighbor relationships after configuration.
- Use route summarization between areas whenever possible.
- Monitor OSPF using `show ip ospf neighbor` and `show ip ospf database`.

---

# Summary

**Open Shortest Path First (OSPF)** is a **link-state dynamic routing protocol** that uses the **Shortest Path First (Dijkstra) algorithm** to determine the most efficient path through a network. Unlike RIP, which relies on **hop count**, OSPF selects routes using **cost**, providing more intelligent path selection and faster convergence. OSPF supports **hierarchical network design** through areas, elects **Designated Routers (DRs)** and **Backup Designated Routers (BDRs)** on multi-access networks to reduce routing overhead, and scales efficiently from small to very large enterprise environments. These capabilities make OSPF one of the most widely deployed routing protocols in modern IP networks.

---

[Static Routing](../../04-routing-technologies/static-routing/README.md) · [RIP](../../04-routing-technologies/RIP/README.md) <br><br> 
[⬅ Routing Technologies (Static, RIP, OSPF)](../../04-routing-technologies/README.md) 
