# 4. Routing Technologies (Static, RIP, OSPF)

[⬅ Back to Home](../../README.md) · [← Previous](../03-switching-technologies/README.md) · [Next →](../05-network-services/README.md)

---

## Overview
<p align="justify">
This section introduces the routing technologies that allow data to move efficiently across networks. Static routing provides manual control, while dynamic routing protocols like RIP and OSPF automate path selection and adapt to network changes. Understanding these methods is critical for designing resilient and scalable enterprise networks.
</p>
---

## Topics Covered
- Static Routing
  - Manual route configuration
  - Advantages and limitations
- Routing Information Protocol (RIP)
  - Distance-vector protocol basics
  - Hop count metric
  - RIP v1 vs RIP v2
- Open Shortest Path First (OSPF)
  - Link-state protocol fundamentals
  - Cost metric calculation
  - Areas and hierarchical design
  - DR/BDR election
- Comparing Static, RIP, and OSPF
- Troubleshooting routing issues

---

## Notes & Diagrams
- **Static Routing**: Best for small, stable networks. Requires manual updates when topology changes.
- **RIP**: Simple to configure but limited scalability (max hop count = 15).
- **OSPF**: Scalable and efficient, supports large enterprise networks with hierarchical area design.

- **Diagram Idea**:  
  - Show a small network with static routes configured manually.  
  - Another diagram with RIP exchanging routes automatically.  
  - A larger enterprise topology with OSPF areas (Area 0 backbone, Area 1, Area 2).

*(Add Packet Tracer or Visio diagrams here to visualize routing behavior.)*

---

## Resources
- 📖 [Cisco Networking Academy: Routing Technologies](https://www.netacad.com/courses/networking)
- 📘 RFC 1058: Routing Information Protocol
- 📘 RFC 2328: OSPF Version 2 Specification
- 🎥 YouTube: “Static vs Dynamic Routing” tutorials
- 🧩 Cisco Packet Tracer labs for RIP and OSPF practice

---


[⬅ Back to Home](../../README.md) · [← Previous](../03-switching-technologies/README.md) · [Next →](../05-network-services/README.md)
