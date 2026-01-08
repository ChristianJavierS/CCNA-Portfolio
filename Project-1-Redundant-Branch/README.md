# Project 1: Redundant Enterprise Branch Office Deployment

## Project Overview
This project demonstrates the implementation of a high-availability (HA) network architecture for a branch office. The design utilizes a **Two-Tier Collapsed Core** model to ensure that the network remains functional even during hardware or link failures.

### Objective
To eliminate single points of failure at the Access and Distribution layers, ensuring that the Sales (VLAN 10) and HR (VLAN 20) departments maintain 99.9% connectivity.

---

## Technologies & Protocols
* **HSRP (Hot Standby Router Protocol):** Provides first-hop redundancy by creating a virtual gateway IP shared between two multilayer switches.
* **LACP EtherChannel (802.3ad):** Bundles multiple physical links into a single logical channel to increase bandwidth and provide link-level redundancy.
* **Rapid-PVST+ (802.1w):** Ensures fast spanning-tree convergence (under 2 seconds) by immediately transitioning ports to the forwarding state upon topology changes.
* **Inter-VLAN Routing (SVI):** Configured on Multilayer Switches to handle internal traffic routing between departments efficiently.
* **OSPF:** Configured on all layer three devices to ensure dynamic routing and path reliability. 
* **NAT:** Configured NAT to provide internet access through the ISPs connections.
---

## Topology & Design
The network is built in a **Full Mesh** between the Access and Core layers:
1.  **Core Layer:** Two Cisco 3650 Multilayer Switches acting as the "Heart" of the network.
2.  **Access Layer:** Dual-homed Cisco 2960 switches connecting end-users.
3.  **Edge Layer:** Cisco 2911 Routers providing WAN connectivity.

> **Design Choice:** I chose a Collapsed Core design to reduce latency and physical equipment costs while maintaining the same level of redundancy as a three-tier model.

---

## Addressing Plan
| VLAN | Department | Subnet | Gateway (Virtual IP) |
| :--- | :--- | :--- | :--- |
| 10 | Sales | 192.168.10.0/24 | 192.168.10.1 |
| 20 | HR | 192.168.20.0/24 | 192.168.20.1 |
| 99 | Management | 192.168.99.0/24 | 192.168.99.1 |

---

## Verification & Failover Results
To prove the resilience of this design, I conducted the following tests:

1.  **EtherChannel Verification:**
    * Command: `show etherchannel summary`
    * Result: Both links bundled successfully (Protocol: LACP, State: P).
2.  **HSRP Failover Test:**
    * Action: Administratively shut down the Active MLS for VLAN 10.
    * Observation: Standby MLS took over the Virtual IP in ~1.5 seconds.
    * Proof: Continuous ping from PC0 to Gateway dropped only 1 packet.
3.  **STP Root Alignment:**
    * Verified that the Root Bridge for VLAN 10 is the HSRP Active switch to prevent sub-optimal traffic paths.

---

## Challenges & Troubleshooting
* **Challenge:** Initially, VLANs could not communicate even though SVIs were "Up/Up."
* **Solution:** Discovered that `ip routing` is disabled by default on Cisco 3650 switches in Packet Tracer. Running the `ip routing` global command resolved the issue and enabled inter-VLAN traffic.

* **Challenge:** After configuring layer 2 and 3 devices, ping from PC1 was requesting time out when pinging 8.8.8.8 . 
* **Solution:** Discovered that NAT needed to be set up. After configuring the NAT on the Edge Routers the PC1 properly was able to get the appropiate response.

---

## Files in this Folder
* `Branch-Office-Lab.pkt`: The original Packet Tracer file.
* `Topology-Diagram.png`: High-resolution network diagram.
* `Device-Configs/`: Folder containing sanitized startup configurations for all devices.
