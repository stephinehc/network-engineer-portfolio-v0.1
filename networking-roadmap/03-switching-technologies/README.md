# 3. Switching Technologies (STP, EtherChannel)

[⬅ Back to Home](../../README.md) · [← Previous](../02-lan-technologies/README.md) · [Next →](../04-routing-technologies/README.md)

---

## Overview
<p align="justify">
This section explores the switching technologies that ensure redundancy, loop prevention, and efficient use of bandwidth in enterprise LANs. Spanning Tree Protocol (STP) prevents broadcast storms caused by loops, while EtherChannel aggregates multiple physical links into one logical channel for higher throughput and fault tolerance.
</p>
---

## Topics Covered
- Spanning Tree Protocol (STP)
  - Purpose and operation
  - Root bridge election
  - Port states (Blocking, Listening, Learning, Forwarding)
  - Rapid Spanning Tree Protocol (RSTP) enhancements
- EtherChannel
  - Benefits of link aggregation
  - Configuration methods (PAgP, LACP, static)
  - Load balancing options
  - Troubleshooting EtherChannel misconfigurations

---

## Notes & Diagrams
- **STP Key Point**: Prevents loops by blocking redundant paths while keeping one active path to the root bridge.
- **RSTP Advantage**: Faster convergence compared to traditional STP.
- **EtherChannel Example**:  
  - Four FastEthernet links bundled into one logical channel → higher bandwidth and redundancy.

- **Diagram Idea**:  
  - Show two switches connected with multiple links.  
  - Without EtherChannel → STP blocks redundant links.  
  - With EtherChannel → all links are bundled and used simultaneously.

*(Add Packet Tracer or Visio diagrams here to visualize STP and EtherChannel operation.)*

---

## Resources
- 📖 [Cisco Networking Academy: Switching Technologies](https://www.netacad.com/courses/networking)
- 📘 IEEE 802.1D Standard (STP)
- 📘 IEEE 802.3ad Standard (LACP for EtherChannel)
- 🎥 YouTube: “STP Explained” and “EtherChannel Configuration” tutorials
- 🧩 Cisco Packet Tracer labs for STP and EtherChannel practice

---

[⬅ Back to Home](../../README.md) · [← Previous](../02-lan-technologies/README.md) · [Next →](../04-routing-technologies/README.md)
