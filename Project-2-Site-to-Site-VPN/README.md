# Project 2: Site-to-Site VPN

## Project Overview
This project demonstrates the implementation of a secure, encrypted "tunnel" over an untrusted public network (ISP) to connect a Corporate Headquarter (HQ) to a remote Branch office. By combining GRE (Generic Routing Encapsulation) with IPsec (Internet Protocol Security), the design allows for both private data encryption and the transmission of dynamic routing protocol traffic.

### Objective
To establish a secure, transparent communication path between two geographically distant LANs, ensuring data confidentiality and integrity.

---

## Technologies & Protocols
* **GRE Tunneling:** Used to encapsulate various protocol packet types inside IP tunnels, allowing the transmission of routing protocol multicast traffic over the WAN.
* **IPsec (AES-256/SHA):** Provides industry-standard encryption and authentication to protect data as it traverses the public internet.
* **ISAKMP (IKE Phase 1):** Manages the secure handshake and key exchange between the HQ and Branch routers using a pre-shared key.
* **IPsec Transform Sets (Phase 2):** Defines the specific encryption parameters (ESP-AES-256, ESP-SHA-HMAC) for the data payload.
* **Cisco IOS Security License:** Activated the securityk9 technology package on ISR 2911 routers to enable cryptographic features.

---

## Topology & Design
The architecture simulates a real-world WAN deployment across an untrusted medium:
1.  **Edge Layer:** Two Cisco 2911 Routers (HQ & Branch) acting as VPN Terminators.
2.  **Provider Layer:** A central Cisco Router simulating an ISP with public IP addressing.
3.  **Internal Layer:** End-devices in separate subnets requiring secure, private communication.

---

## Addressing Plan
| Entity | Interface | IP Address | Purpose |
| :--- | :--- | :--- | :--- |
| HQ LAN | g0/1 | 192.168.10.0/24 | Gateway for HQ users |
| Branch LAN | g0/1 | 192.168.20.0/24 | Gateway for Branch users |
| Tunnel 0 | Tun0 | 10.0.0.x/30 | Virtual link for HQ-Branch routing |
| ISP Link (HQ) | s0/3/0 | 207.43.1.2/29 | Public WAN Interface (HQ) |
| ISP Link (Branch) | s0/3/0 | 207.43.2.2/29 | Public WAN Interface (Branch) |

---

## Verification & Security Results
To validate the security and functionality of the tunnel, I conducted the following tests:

1.  **ISAKMP Status:**
    * Command: `show crypto isakmp sa`
    * Result: State shows QM_IDLE. This confirms the Phase 1 handshake is successful and the secure management channel is active.
2.  **Encryption Verification:**
    * Command: `show crypto ipsec sa`
    * Observation: Verified that the #pkts encaps and #pkts decaps counters increased only when internal LAN traffic was initiated.
    * Proof: Captured packets in Packet Tracer's Simulation Mode showed the original payload was completely hidden by ESP (Encapsulating Security Payload) headers.


---

## Challenges & Troubleshooting
* **Challenge:** The crypto isakmp commands were missing from the router's CLI during initial configuration.
* **Solution:** Identified that the Cisco 2911 ISR boots with a base license. I applied the `license boot module c2900 technology-package securityk9` command and reloaded the device to enable the encryption engine.


---

## Files in this Folder
* `Branch-Office-Lab.pkt`: The original Packet Tracer file.
* `Topology-Diagram.png`: High-resolution network diagram.
* `Device-Configs/`: Folder containing sanitized startup configurations for all devices.
