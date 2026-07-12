# 8. Secure WAN (GRE over IPsec VPN)

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br>
[← Previous](../07-high-availability/README.md) · [Next →](../09-enterprise-campus-network-concepts/README.md)

---

## Overview
<p align="justify">
This section focuses on secure Wide Area Network (WAN) connectivity using tunneling and encryption technologies. Generic Routing Encapsulation (GRE) provides a way to encapsulate packets for transport across networks, while IPsec ensures confidentiality, integrity, and authentication. Combining GRE with IPsec allows organizations to securely extend their networks across untrusted infrastructures like the internet.
</p>
---

## Topics Covered
- WAN security challenges
- GRE (Generic Routing Encapsulation)
  - Purpose and operation
  - Encapsulation of routing protocols
  - GRE tunnel configuration
- IPsec (Internet Protocol Security)
  - Encryption and authentication
  - Security associations (SA)
  - Internet Key Exchange (IKE)
- GRE over IPsec
  - Benefits of combining GRE and IPsec
  - Configuration steps
  - Use cases in enterprise WANs
- Troubleshooting secure tunnels

---

## Notes & Diagrams
- **GRE Example**:  
  - Allows routing protocols (like OSPF) to run across a tunnel between two sites.  

- **IPsec Example**:  
  - Encrypts traffic between two routers to protect data across the public internet.  

- **GRE over IPsec**:  
  - GRE provides routing flexibility, IPsec secures the traffic.  
  - Commonly used for site-to-site VPNs in enterprises.  

- **Diagram Idea**:  
  - Show two branch routers connected via the internet.  
  - GRE tunnel established between them.  
  - IPsec applied to secure GRE traffic.  

*(Add Packet Tracer or Visio diagrams here to visualize GRE over IPsec operation.)*

---

## Resources
- 📖 [Cisco Networking Academy: WAN and VPN Technologies](https://www.netacad.com/courses/networking)
- 📘 RFC 2784: Generic Routing Encapsulation (GRE)
- 📘 RFC 4301: Security Architecture for IPsec
- 🎥 YouTube: “GRE over IPsec Configuration” tutorials
- 🧩 Cisco Packet Tracer labs for GRE and IPsec practice

---

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br>
[← Previous](../07-high-availability/README.md) · [Next →](../09-enterprise-campus-network-concepts/README.md)
