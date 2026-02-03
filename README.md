# Hospital Network Lab (VLAN Segmentation)

This repository documents a virtualized hospital network lab designed to demonstrate
VLAN-based segmentation, access-layer switching, and inter-VLAN routing using Cisco
network equipment and virtual machines.

The goal of this lab is to model a realistic small-to-medium hospital network architecture
with clear separation between clinical, administrative, and guest systems.

---

## Hardware Setup

### Computer (Hypervisor Host)

The computer used for this lab is an **HP EliteDesk 800** running **Ubuntu 24.04.3 LTS**.
It serves as the hypervisor host for all virtual machines.

**Specifications:**
- Intel Core i5-7500
- 12 GB DDR4 RAM
- 500 GB HDD
- 256 GB SSD
- Quad-port network interface card (used to map VMs to individual VLANs)

---

### Switch

The access-layer switch used in this lab is a **Cisco Catalyst 3560 PoE-24**.

**Features:**
- 24 Ethernet ports
- Layer 3 switching capability
- Power over Ethernet (PoE) support (not required for this lab)

The switch is responsible for VLAN separation and acts as the access layer for all virtual
machines.

---

### Router

The router used for inter-VLAN routing is a **Cisco 2900 Series Integrated Services Router (ISR)**.

The router provides:
- Default gateways for each VLAN
- Inter-VLAN routing via an 802.1Q trunk
- An upstream routing point for future WAN or firewall integration

---

## Network Architecture

The network is designed with strict Layer 2 and Layer 3 separation:

- Virtual machines connect to the network via dedicated physical NICs on the hypervisor
- Each NIC connects to a switch access port assigned to a unique VLAN
- The Cisco switch handles VLAN segmentation
- The router performs all inter-VLAN routing using a trunk link

```mermaid
flowchart LR

    classDef vlan10 fill:#b8daff,color:#000000,stroke:#004085,stroke-width:2px
    classDef vlan20 fill:#c3e6cb,color:#000000,stroke:#155724,stroke-width:2px
    classDef vlan30 fill:#ffeeba,color:#000000,stroke:#856404,stroke-width:2px
    classDef vlan40 fill:#f5c6cb,color:#000000,stroke:#721c24,stroke-width:2px

    classDef infra fill:#ced4da,color:#000000,stroke:#343a40,stroke-width:2px
    classDef core fill:#bee5eb,color:#000000,stroke:#0c5460,stroke-width:2px

    subgraph Hypervisor
        VM1["VM1<br/>EHR System<br/>VLAN 10"]:::vlan10
        VM2["VM2<br/>Imaging<br/>VLAN 20"]:::vlan20
        VM3["VM3<br/>Pharmacy<br/>VLAN 30"]:::vlan30
        VM4["VM4<br/>Guest / IoT<br/>VLAN 40"]:::vlan40
    end

    NIC1["NIC 1"]:::infra
    NIC2["NIC 2"]:::infra
    NIC3["NIC 3"]:::infra
    NIC4["NIC 4"]:::infra

    Switch["Cisco Catalyst 3560"]:::infra
    Router["Cisco 2900 ISR<br/>Inter-VLAN Routing"]:::core

    VM1 --> NIC1 -->|"Access VLAN 10"| Switch
    VM2 --> NIC2 -->|"Access VLAN 20"| Switch
    VM3 --> NIC3 -->|"Access VLAN 30"| Switch
    VM4 --> NIC4 -->|"Access VLAN 40"| Switch

    Switch -->|"802.1Q Trunk"| Router

