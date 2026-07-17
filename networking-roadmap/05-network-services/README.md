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

## Notes & Diagrams
- **DHCP Example**:  
  - Client requests an IP → DHCP server assigns `192.168.1.10` with subnet mask, gateway, and DNS info.  

- **DNS Example**:  
  - `www.example.com` → resolved to `93.184.216.34`.  

- **TFTP Use Case**:  
  - Router boots and retrieves its configuration file from a TFTP server.  

- **Diagram Idea**:  
  - Show a client obtaining an IP from a DHCP server.  
  - Show DNS resolving a domain name.  
  - Show a router pulling a config file from a TFTP server.  

*(Add Packet Tracer or Visio diagrams here to visualize DHCP, DNS, and TFTP operations.)*

---

## Resources
- 📖 [Cisco Networking Academy: Network Services](https://www.netacad.com/courses/networking)
- 📘 RFC 2131: Dynamic Host Configuration Protocol
- 📘 RFC 1035: Domain Name System Implementation
- 📘 RFC 1350: Trivial File Transfer Protocol
- 🎥 YouTube: “DHCP Explained” and “DNS Basics” tutorials
- 🧩 Cisco Packet Tracer labs for DHCP, DNS, and TFTP practice

---

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br> [← Previous](../04-routing-technologies/README.md) · [Next →](../06-network-security/README.md)
