# 5. Network Services (DHCP, DNS, TFTP)

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br> [← Previous](../04-routing-technologies/README.md) · [Next →](../06-network-security/README.md)

---

## Overview
<p align="justify">
This section introduces the essential network services that support connectivity and communication in enterprise environments. DHCP automates IP address assignment, DNS translates human-readable names into IP addresses, and TFTP provides lightweight file transfer capabilities. Together, these services simplify network operations and improve efficiency.
</p>

---

## Topics Covered
- Dynamic Host Configuration Protocol (DHCP)
  - Purpose and operation
  - DHCP lease process (DORA: Discover, Offer, Request, Acknowledge)
  - Configuring DHCP on routers and servers
- Domain Name System (DNS)
  - Name resolution process
  - Forward and reverse lookup zones
  - DNS hierarchy and caching
- Trivial File Transfer Protocol (TFTP)
  - Lightweight file transfer
  - Common uses (IOS image upgrades, configuration backups)
  - Limitations compared to FTP/SFTP

---

# Network Services

Network services provide essential functions that enable devices to communicate efficiently on a network. Three of the most commonly used network services are:

- **Dynamic Host Configuration Protocol (DHCP)** – Automatically assigns IP addressing information to devices.
- **Domain Name System (DNS)** – Translates domain names into IP addresses.
- **Trivial File Transfer Protocol (TFTP)** – Transfers files, primarily for network device management.

---

# Dynamic Host Configuration Protocol (DHCP)

The **Dynamic Host Configuration Protocol (DHCP)** is a network protocol that automatically assigns IP configuration information to devices connected to a network. Instead of manually configuring every device, DHCP simplifies network administration by dynamically providing network settings.

---

## Purpose and Operation

DHCP automatically assigns the following network information to clients:

- IP Address
- Subnet Mask
- Default Gateway
- DNS Server
- Lease Duration

A device configured as a **DHCP Client** requests an IP address from a **DHCP Server** whenever it joins the network.

Benefits of DHCP include:

- Automatic IP address assignment
- Reduced administrative workload
- Prevents duplicate IP addresses
- Simplifies network management

---

## DHCP Lease Process (DORA)

The DHCP address assignment process consists of four steps known as **DORA**.

### 1. Discover

The client broadcasts a **DHCP Discover** message to locate available DHCP servers.

### 2. Offer

A DHCP server responds with a **DHCP Offer** containing an available IP address and other network configuration.

### 3. Request

The client sends a **DHCP Request** indicating which offered address it wishes to use.

### 4. Acknowledge (ACK)

The DHCP server confirms the lease by sending a **DHCP Acknowledge (ACK)** message.

```text
Client                  DHCP Server
   |------Discover------->|
   |<-------Offer---------|
   |------Request-------->|
   |<----Acknowledge------|
```

---

## Configuring DHCP on Cisco Routers

Example DHCP configuration:

```bash
Router(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.20

Router(config)# ip dhcp pool LAN
Router(dhcp-config)# network 192.168.10.0 255.255.255.0
Router(dhcp-config)# default-router 192.168.10.1
Router(dhcp-config)# dns-server 8.8.8.8
Router(dhcp-config)# domain-name company.local
```

Useful verification commands:

```bash
show ip dhcp binding
show ip dhcp pool
show running-config
```

---

## Configuring DHCP on Servers

A dedicated DHCP server (such as Windows Server or Linux) can also provide DHCP services.

Typical configuration includes:

- DHCP Scope
- Starting IP Address
- Ending IP Address
- Subnet Mask
- Default Gateway
- DNS Server
- Lease Duration

Dedicated DHCP servers are commonly used in medium to large enterprise networks.

---

# Domain Name System (DNS)

The **Domain Name System (DNS)** is a distributed naming service that translates human-readable **domain names** into **IP addresses**.

Instead of remembering numerical IP addresses, users can access resources using domain names such as:

```text
www.google.com
```

DNS translates that name into its corresponding IP address.

---

## Name Resolution Process

When a user enters a domain name into a web browser:

1. The client checks its local DNS cache.
2. If the record is not found, the request is sent to the configured DNS server.
3. If necessary, the DNS server queries other DNS servers.
4. The correct IP address is returned.
5. The client connects to the destination.

Example:

```text
www.example.com
        │
        ▼
    DNS Server
        │
        ▼
  93.184.216.34
```

---

## Forward and Reverse Lookup Zones

### Forward Lookup Zone

Resolves:

```text
Host Name  →  IP Address
```

Example:

```text
www.company.local

↓

192.168.10.15
```

---

### Reverse Lookup Zone

Resolves:

```text
IP Address  →  Host Name
```

Example:

```text
192.168.10.15

↓

www.company.local
```

Reverse lookup zones are commonly used for:

- Troubleshooting
- Logging
- Email server verification
- Network management

---

## DNS Hierarchy and Caching

DNS is organized into a hierarchical structure.

```text
                Root (.)
                   │
         Top-Level Domain (.com)
                   │
            example.com
                   │
          www.example.com
```

To improve performance, DNS uses **caching**.

When a domain name is resolved, the result is temporarily stored in memory. Future requests for the same domain can be answered directly from the cache, reducing lookup time and network traffic.

---

# Trivial File Transfer Protocol (TFTP)

The **Trivial File Transfer Protocol (TFTP)** is a simple file transfer protocol that operates over **UDP port 69**. It is designed for lightweight file transfers and is commonly used for managing network devices.

Unlike FTP or SFTP, TFTP provides no authentication or encryption.

---

## Lightweight File Transfer

Characteristics of TFTP:

- Uses UDP instead of TCP
- Simple implementation
- Low protocol overhead
- Fast file transfers within trusted networks
- No authentication
- No encryption

---

## Common Uses

TFTP is commonly used for:

- Cisco IOS image upgrades
- Router configuration backups
- Switch configuration backups
- Restoring device configurations
- Firmware upgrades
- PXE network booting

Example Cisco commands:

Backup running configuration:

```bash
copy running-config tftp
Address or name of remote host []? <IP address of the TFTP server>
```

Restore configuration:

```bash
copy tftp running-config
Address or name of remote host []? <IP address of the TFTP server>
```

Backup IOS image:

```bash
copy flash: tftp
```

---

## Limitations Compared to FTP and SFTP

| Feature | TFTP | FTP | SFTP |
|----------|------|-----|------|
| Transport Protocol | UDP | TCP | SSH (TCP) |
| Authentication | No | Yes | Yes |
| Encryption | No | No | Yes |
| Reliability | Low | High | High |
| Security | Low | Moderate | High |
| Common Use | Network device maintenance | General file transfer | Secure file transfer |

> **Note:** TFTP should only be used on **trusted local networks** because it does not provide authentication or encryption. For secure file transfers over untrusted networks, **SFTP** is the recommended protocol.

# Example Scenario
[Scenario](../05-network-services/net-service/README.md)

---

# Summary

- **DHCP** automatically assigns IP addresses and other network settings to client devices using the **DORA** process (Discover, Offer, Request, Acknowledge).
- **DNS** translates domain names into IP addresses through a hierarchical and distributed naming system, improving usability and reducing the need to remember IP addresses.
- **TFTP** is a lightweight file transfer protocol primarily used for backing up and restoring network device configurations and transferring IOS images. While simple and efficient, it lacks authentication and encryption, making it suitable only for trusted environments.


---

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br> [← Previous](../04-routing-technologies/README.md) · [Next →](../06-network-security/README.md)
