Project 2: Site to Site VPN
Project Overview

This project demonstrates the implementation of a secure, encrypted "tunnel" over an untrusted public network (ISP) to connect a Corporate Headquarter (HQ) to a remote Branch office. By combining GRE (Generic Routing Encapsulation) with IPsec (Internet Protocol Security), the design allows for both private data encryption and the transmission of dynamic routing protocol traffic.
Objective

To establish a secure, transparent communication path between two geographically distant LANs, ensuring data confidentiality, integrity, and the ability to scale using dynamic routing (OSPF).
Technologies & Protocols

    GRE Tunneling: Used to encapsulate various protocol packet types inside IP tunnels, allowing the use of routing protocols over the WAN.

    IPsec (AES-256/SHA): Provides high-level encryption and authentication to protect data as it traverses the public internet.

    ISAKMP (IKE Phase 1): Manages the secure handshake and key exchange between the HQ and Branch routers.

    IPsec Transform Sets (Phase 2): Defines the specific encryption parameters (ESP-AES, ESP-SHA-HMAC) for the data payload.

    Cisco IOS Security License: Activated the securityk9 technology package on ISR 2911 routers to enable cryptographic features.

Topology & Design

The architecture simulates a real-world WAN deployment:

    Edge Layer: Two Cisco 2911 Routers (HQ & Branch) acting as VPN Terminators.

    Provider Layer: A single Cisco Router simulating an ISP with public IP addressing.

    Internal Layer: End-devices in separate subnets requiring secure communication.

Design Choice: I chose GRE over IPsec rather than a pure IPsec VPN because pure IPsec does not support multicast or broadcast traffic. By using GRE as the transport, I enabled OSPF to form neighbor adjacencies across the tunnel, allowing the network to scale automatically if more subnets are added.
Addressing Plan
Entity	Interface	IP Address	Purpose
HQ LAN	G0/1	192.168.10.1/24	Gateway for HQ Users
Branch LAN	G0/1	192.168.20.1/24	Gateway for Branch Users
Tunnel 0	Tun0	10.0.0.x/30	Virtual link for HQ-Branch
ISP Link (HQ)	G0/0	207.43.1.2/29	Public WAN Interface
ISP Link (BR)	G0/0	207.43.2.2/29	Public WAN Interface
Verification & Security Results

To validate the security and functionality of the tunnel:

    ISAKMP Status:

        Command: show crypto isakmp sa

        Result: QM_IDLE (Confirmed that the Phase 1 handshake is successful and stable).

    Encryption Verification:

        Command: show crypto ipsec sa

        Observation: #pkts encaps and #pkts decaps increased only when internal LAN traffic was sent.

        Proof: Captured packets in Simulation Mode showed the original payload was replaced by ESP (Encapsulating Security Payload) headers.

    Routing Table:

        Verified that the Branch LAN (192.168.20.0) appears in the HQ routing table with a next-hop of the Tunnel interface (10.0.0.2), not the ISP interface.

Challenges & Troubleshooting

    Challenge: The crypto isakmp commands were rejected by the CLI.

        Solution: Identified that the 2911 ISR requires a manual license boot. Applied license boot module c2900 technology-package securityk9, saved the config, and reloaded the device.



Files in this Folder

    VPN-Site-to-Site.pkt: The Packet Tracer lab file.

    VPN-Topology.png: Logic diagram showing the crypto-map application points.

    Configs/: Sanitized text files of the HQ, Branch, and ISP configurations.
