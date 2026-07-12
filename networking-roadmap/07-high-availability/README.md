# 7. High Availability (HSRP)

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../networking-roadmap/README.md) <br><br> [← Previous](../06-network-security/README.md) · [Next →](../08-secure-wan/README.md)

---

## Overview
<p align="justify">
This section introduces the concept of high availability in enterprise networks, focusing on the Hot Standby Router Protocol (HSRP). HSRP ensures network resilience by providing a virtual default gateway that remains available even if one router fails. This redundancy minimizes downtime and guarantees continuous connectivity for end users.
</p>
---

## Topics Covered
- High availability principles in networking
- Hot Standby Router Protocol (HSRP)
  - Purpose and operation
  - Active and standby router roles
  - Virtual IP and MAC addresses
- HSRP states (Initial, Learn, Listen, Speak, Standby, Active)
- Priority and preemption configuration
- Load balancing with multiple HSRP groups
- Troubleshooting HSRP issues

---

## Notes & Diagrams
- **HSRP Example**:  
  - Two routers configured with HSRP share a virtual IP (`192.168.1.1`).  
  - Router A is active, Router B is standby.  
  - If Router A fails, Router B takes over seamlessly.  

- **HSRP States**:  
  - Active router forwards traffic.  
  - Standby router monitors and takes over if active fails.  

- **Diagram Idea**:  
  - Show two routers connected to a switch, both configured with HSRP.  
  - Clients use the virtual IP as their default gateway.  
  - Highlight failover from active to standby router.  

*(Add Packet Tracer or Visio diagrams here to visualize HSRP operation.)*

---

## Resources
- 📖 [Cisco Networking Academy: High Availability](https://www.netacad.com/courses/networking)
- 📘 Cisco IOS HSRP Configuration Guide
- 📘 RFC 2281: Cisco Hot Standby Router Protocol
- 🎥 YouTube: “HSRP Explained” and “HSRP Configuration” tutorials
- 🧩 Cisco Packet Tracer labs for HSRP practice

---

[⬅ Back to Home](../../README.md) · [⬅ Networking Roadmap](../networking-roadmap/README.md) <br><br> [← Previous](../06-network-security/README.md) · [Next →](../08-secure-wan/README.md)
