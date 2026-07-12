# 1. Network Design Fundamentals (Subnetting & VLSM)

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br> <br>[Next →](../02-lan-technologies/README.md)

---


## Overview
<p align="justify">
This section introduces the foundational concepts of IP addressing, subnetting, and Variable Length Subnet Masking (VLSM). These skills are essential for designing efficient, scalable, and organized networks. Mastery of subnetting ensures proper allocation of IP addresses, while VLSM allows flexible design tailored to specific network requirements.
</p>

---

## Topics Covered
- IPv4 address structure (Network ID, Host ID)
- Subnet masks and CIDR notation
- Subnetting step-by-step process
- Calculating usable host ranges
- Variable Length Subnet Masking (VLSM) for hierarchical design
- Address planning for LANs and WANs
- Common subnetting scenarios in enterprise networks

---

## Notes & Diagrams
- **Subnetting**
  - Case A: If requirement is number of Networks (Subnets)
  - Case B: If requirement is number of Hosts/Subnet

- Case A: If requirement is number of Networks (Subnets)

Example: <br>
Company: EduTech Manufacturing Corp.  <br>
Industry: Educational equipment and technology solutions<br>
They have two major departments that need separate networks:

Production Department
Handles manufacturing of school supplies and tech devices.
Requires a large subnet for factory machines, IoT sensors, and production servers.

Administration Department
Includes HR, Finance, and Management offices.
Needs a smaller subnet for office PCs, printers, and internal applications.

  <img width="660" height="339" alt="image" src="https://github.com/user-attachments/assets/e2646d27-a480-4470-9e83-579327cdd15a" />


- **Subnetting Formula**:  
  - Number of subnets = \(2^n\) (where *n* = borrowed bits)  
  - Hosts per subnet = \(2^h - 2\) (where *h* = host bits)  

- **CIDR Example**:  
  - `192.168.10.0/26` → 4 subnets, 62 hosts each  

- **Diagram Idea**:  
  - Show a network divided into multiple subnets with different mask lengths (e.g., `/24`, `/26`, `/30`) to illustrate VLSM.  

*(Add your own hand-drawn or Visio diagrams here for clarity.)*

---

## Resources
- 📖 [Cisco Networking Academy: Subnetting](https://www.netacad.com/courses/networking)
- 📘 [RFC 950: Internet Standard Subnetting Procedure](https://www.rfc-editor.org/rfc/rfc950)
- 🧮 Online Subnet Calculator: [Subnetting Practice Tool](https://www.subnetting.net)
- 🎥 YouTube: “Subnetting Made Easy” tutorials

---

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../README.md) <br><br> [Next →](../02-lan-technologies/README.md)
