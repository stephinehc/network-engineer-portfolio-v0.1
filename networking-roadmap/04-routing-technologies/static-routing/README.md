
[⬅ Routing Technologies (Static, RIP, OSPF)](../../04-routing-technologies/README.md) <br><br> [RIP](../../04-routing-technologies/RIP/README.md) · [OSPF](../../04-routing-technologies/OSPF/README.md)

---
# Static Routing

**Static routing** is a routing method in which routes are **manually configured** by a network administrator. Unlike dynamic routing protocols such as RIP or OSPF, routers do not automatically exchange routing information. Instead, each destination network must be explicitly configured.

Static routes are commonly used in **small networks**, **stub networks** (networks with only one exit path), or environments where the network topology rarely changes.

---

## How Static Routing Works

When a router receives a packet, it checks its **routing table** to determine the best path toward the destination network.

If a matching **static route** exists, the router forwards the packet to the specified **next-hop IP address** or **exit interface**.

Example:

<img width="819" height="283" alt="image" src="https://github.com/user-attachments/assets/b18f7ac5-0313-4b33-8d82-0f42a4403523" />

We implemented subnetting in the given design and assigned IP addresses on each interfaces.

<img width="832" height="343" alt="image" src="https://github.com/user-attachments/assets/28f1d53b-f1a5-43dd-a8f0-b01c7335a9f4" />

If R1 wants to communicate with the network **192.168.12.64/26**, it must know that packets should be forwarded to R2.

---

## Manual Route Configuration

### Syntax

```bash
Router(config)# ip route <destination-network> <subnet-mask> <next-hop-ip-address | exit-interface>
```

---

### Configure Using the Next-Hop IP Address
Let us implement it on R1.
Suppose Router R1 wants to reach **192.168.12.64/26** through Router R2.

```bash
R1(config)# ip route 192.168.12.64 255.255.255.192 192.168.12.130
```

Explanation:

- Destination Network: **192.168.12.64**
- Subnet Mask: **255.255.255.192**
- Next-Hop Address: **192.168.12.130**

Whenever R1 receives packets destined for **192.168.12.64/26**, it forwards them to **192.168.12.130**.

<img width="1631" height="554" alt="image" src="https://github.com/user-attachments/assets/4acc5041-27ec-4641-b41e-3ab736997b1d" />

---

### Configure Using the Exit Interface
Let us implement it on R2.
Instead of specifying the next-hop IP address, the outgoing interface may also be configured.

```bash
R2(config)# ip route 192.168.12.0 255.255.255.192 GigabitEthernet0/0
```
The router sends packets directly out of the specified interface.

<img width="1630" height="653" alt="image" src="https://github.com/user-attachments/assets/6639f88b-4ef4-4833-bcd0-f9b1fd6e36da" />

#### Verify connectivity

Configure the IP address of the PCs.

<img width="1078" height="570" alt="image" src="https://github.com/user-attachments/assets/fcfd6717-9b41-4658-a671-847c1ba6dc63" />

Ping from PC-A to PC-B and vice versa.

<img width="1894" height="585" alt="image" src="https://github.com/user-attachments/assets/27538c51-f87c-4191-90ef-99ecf4ce08bf" />


---

### Configure a Default Route

A **default route** is used when no matching route exists in the routing table. It is commonly used by branch offices or home networks that have only one path to the Internet.

Syntax:

```bash
Router(config)# ip route 0.0.0.0 0.0.0.0 <next-hop-ip>
```

Example:

```bash
R1(config)# ip route 0.0.0.0 0.0.0.0 192.168.12.130
```

This route is known as the **Gateway of Last Resort**.

Verify:

```bash
R1# show ip route
```

Example output:

```text
Gateway of last resort is 192.168.12.130
```

---

## Administrative Distance

Every routing source has an **Administrative Distance (AD)**, which indicates the trustworthiness of the route. Lower values are preferred.

Static routes have a default Administrative Distance of:

```text
1
```

Some common Administrative Distances are shown below.

| Route Source | Administrative Distance |
|---------------|------------------------:|
| Connected | 0 |
| Static | 1 |
| EIGRP Summary | 5 |
| External BGP | 20 |
| EIGRP | 90 |
| OSPF | 110 |
| RIP | 120 |
| Unknown | 255 |

> **Note:** Because static routes have a lower Administrative Distance than most dynamic routing protocols, they are preferred whenever both routes exist to the same destination.

---

## Floating Static Routes

A **Floating Static Route** is a backup static route that is only used if the primary route fails.

This is accomplished by assigning a **higher Administrative Distance** than the primary route.

Example:

```bash
R1(config)# ip route 192.168.2.0 255.255.255.0 192.168.13.2 200
```

In this example:

- Primary route → AD = 1
- Backup route → AD = 200

The router uses the backup route only when the primary route becomes unavailable.

---

## Advantages of Static Routing

- Simple to configure for small networks.
- No routing update traffic consumes network bandwidth.
- Lower CPU and memory utilization.
- More secure because routes are manually controlled.
- Predictable routing behavior.
- Ideal for stub networks and default routes.

---

## Limitations of Static Routing

- Poor scalability as the network grows.
- Every route must be configured manually.
- No automatic adaptation to topology changes.
- Link failures require manual reconfiguration unless backup routes are configured.
- Configuration errors can easily cause connectivity problems.

---

## Static Route Verification

Display the routing table:

```bash
show ip route
```

Display only static routes:

```bash
show ip route static
```

Display interface status:

```bash
show ip interface brief
```

Verify connectivity:

```bash
ping <destination-ip>
```

Trace the packet path:

```bash
traceroute <destination-ip>
```

View the running configuration:

```bash
show running-config
```

---

# Common Static Routing Problems and Troubleshooting

| Problem | Possible Cause | Solution |
|----------|----------------|----------|
| Destination network unreachable | Missing static route | Verify using `show ip route` and configure the missing route. |
| Next-hop unreachable | Incorrect next-hop IP address | Verify the IP address and ensure the next-hop router is reachable. |
| Interface down | Physical or administrative issue | Check interface status using `show ip interface brief` and enable the interface with `no shutdown` if necessary. |
| Incorrect subnet mask | Wrong destination network or subnet mask | Verify the configured network address and subnet mask. |
| Default route not working | Missing or incorrect gateway of last resort | Verify the default route configuration using `show ip route`. |
| Routing loop | Incorrect static routes between routers | Review all static route configurations and ensure they point toward the correct next-hop router. |

---

# Best Practices for Static Routing

- Use static routing only in **small or stable networks**.
- Use **default routes** when only one exit path exists.
- Configure **floating static routes** for backup paths.
- Verify connectivity after configuration using `ping` and `traceroute`.
- Document all manually configured routes to simplify maintenance.

---

# Summary

Static routing provides a simple, secure, and predictable method of forwarding packets by manually defining network paths. It performs well in small or stable environments where routes rarely change and minimal routing overhead is desired. However, because routes do not update automatically, static routing becomes difficult to manage in larger or frequently changing networks. For enterprise environments with many routers, dynamic routing protocols such as **RIP**, **OSPF**, or **EIGRP** are generally more suitable.

---

[RIP](../../04-routing-technologies/RIP/README.md) · [OSPF](../../04-routing-technologies/OSPF/README.md) <br><br> 
[⬅ Routing Technologies (Static, RIP, OSPF)](../../04-routing-technologies/README.md) 
