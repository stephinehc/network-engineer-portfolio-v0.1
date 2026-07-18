# 6. Network Security (ACLs, NAT)

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br> [← Previous](../05-network-services/README.md) · [Next →](../07-high-availability/README.md)

---


## Overview
<p align="justify">
This section introduces the fundamental security mechanisms used in enterprise networks. Access Control Lists (ACLs) provide traffic filtering and policy enforcement, while Network Address Translation (NAT) enables private IP addressing schemes to communicate with external networks securely. Together, ACLs and NAT form the backbone of perimeter defense and traffic management.
</p>

---

## Topics Covered
- Access Control Lists (ACLs)
  - Standard vs Extended ACLs
  - Numbered vs Named ACLs
  - Wildcard masks
  - Placement rules (inbound vs outbound)
  - Common use cases (restricting access, filtering traffic)
- Network Address Translation (NAT)
  - Static NAT
  - Dynamic NAT
  - Port Address Translation (PAT)
  - NAT configuration and troubleshooting
- Security considerations and best practices

---

## Notes & Diagrams
- **ACL Example**:  
  - Standard ACL: `access-list 1 permit 192.168.1.0 0.0.0.255`  
  - Extended ACL: `access-list 100 deny tcp any host 192.168.1.10 eq 80`  

- **NAT Example**:  
  - Static NAT: `ip nat inside source static 192.168.1.10 203.0.113.10`  
  - PAT: Multiple internal hosts share a single public IP using different port numbers.  

- **Diagram Idea**:  
  - Show a router with ACLs applied to an interface controlling inbound/outbound traffic.  
  - Show NAT translating private IPs to public IPs for internet access.  

*(Add Packet Tracer or Visio diagrams here to visualize ACL and NAT operations.)*

---

## Resources
- 📖 [Cisco Networking Academy: Network Security Basics](https://www.netacad.com/courses/networking)
- 📘 RFC 1631: The Original NAT Specification
- 📘 Cisco IOS Security Configuration Guide
- 🎥 YouTube: “ACL Configuration” and “NAT Explained” tutorials
- 🧩 Cisco Packet Tracer labs for ACLs and NAT practice

---


[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br> [← Previous](../05-network-services/README.md) · [Next →](../07-high-availability/README.md)
