# Network Fundamentals & Subnetting Design — BlueWave Industries Ltd.

A network design proposal for a mid-sized company (case-study scenario), written for the
**HND in Computing — Networking unit (Assignment 1 of 2)** at GUSTO College, December 2025.

The scenario: as a junior network technician at *TechNova Network Solutions*, design
the network for **BlueWave Industries Ltd.**, a growing company with **6 departments,
79 users**, and shared printers, servers, VoIP phones and wireless access points,
with room to grow by 30%.

📄 **[Read the full report (PDF, 64 pages)](report/BlueWave-Network-Design-Report.pdf)**
📊 **[Addressing & subnetting tables (Excel)](tables/Addressing-and-Subnetting-Tables.xlsx)**

---

## What I did

**Part I — Networking principles**
- Compared network types (LAN, WAN, WLAN, MAN, PAN) for this business
- Covered the relevant standards: IEEE 802.3 (Ethernet), 802.11 (Wi-Fi), 802.1Q (VLANs), TCP/IP
- Analysed star, mesh, bus, ring and hybrid topologies and **recommended a switched star topology** with VLAN segmentation
- Explained the key protocols (TCP/IP, DHCP, DNS, HTTP/HTTPS, FTP, SNMP, SMTP, SSH, NTP, ARP) and mapped protocols and devices to the **OSI and TCP/IP models**
- Discussed switches, routers, access points, firewalls, servers and how workstation hardware and networking software depend on each other

**Part II — IP addressing & subnetting**
- Calculated per-department host requirements, including 30% growth
- Designed a **VLSM** subnetting scheme in the private range `192.168.0.0/16` (RFC 1918)
- Worked through the subnet calculations for each department by hand
- Mapped each subnet to a VLAN, planned static vs. DHCP ranges, and evaluated security (ACLs, firewalling for Finance/HR) and QoS (VoIP prioritisation)

---

## Requirement analysis

| Department | Users | Printers | VoIP | APs | Servers | Growth (30%) | **Total hosts** | Subnet |
|---|---:|---:|---:|---:|---:|---:|---:|---|
| Sales & Marketing | 25 | 2 | 25 | 2 | 1 | 17 | **72** | /25 |
| Administration | 18 | 2 | 18 | 1 | 1 | 12 | **52** | /26 |
| Finance | 12 | 1 | 12 | 1 | 2 | 8 | **36** | /26 |
| Human Resources | 10 | 1 | 10 | 1 | 1 | 7 | **30** | /27 |
| IT Support | 8 | 1 | 8 | 1 | 5 | 7 | **30** | /27 |
| Management | 6 | 1 | 6 | 1 | 1 | 5 | **20** | /27 |
| **Total** | 79 | 8 | 79 | 7 | 11 | 56 | **240** | |

## IP addressing table (VLSM)

Subnets were allocated largest-first so every block falls on a clean binary boundary with no gaps or overlaps.

| VLAN | Department | Network | Mask | Usable range | Broadcast | Gateway | Usable | Used | Spare |
|---|---|---|---|---|---|---|---:|---:|---:|
| 10 | Sales & Marketing | 192.168.0.0/25 | 255.255.255.128 | .0.1 – .0.126 | 192.168.0.127 | 192.168.0.1 | 126 | 72 | 54 |
| 20 | Administration | 192.168.0.128/26 | 255.255.255.192 | .0.129 – .0.190 | 192.168.0.191 | 192.168.0.129 | 62 | 52 | 10 |
| 30 | Finance | 192.168.0.192/26 | 255.255.255.192 | .0.193 – .0.254 | 192.168.0.255 | 192.168.0.193 | 62 | 36 | 26 |
| 40 | Human Resources | 192.168.1.0/27 | 255.255.255.224 | .1.1 – .1.30 | 192.168.1.31 | 192.168.1.1 | 30 | 30 | 0 |
| 50 | IT Support | 192.168.1.32/27 | 255.255.255.224 | .1.33 – .1.62 | 192.168.1.63 | 192.168.1.33 | 30 | 30 | 0 |
| 60 | Management | 192.168.1.64/27 | 255.255.255.224 | .1.65 – .1.94 | 192.168.1.95 | 192.168.1.65 | 30 | 20 | 10 |

**Result:** 240 required hosts fit into 340 usable addresses (~71% utilisation), using only
`192.168.0.0 – 192.168.1.95` and leaving the rest of the private range free for expansion.

### How each subnet size was chosen

Smallest `n` (host bits) such that `2^n − 2 ≥ required hosts`:

| Department | Hosts needed | Calculation | Prefix | Mask (binary, last octet) |
|---|---:|---|---|---|
| Sales & Marketing | 72 | 2⁷ − 2 = 126 | /25 | `10000000` |
| Administration | 52 | 2⁶ − 2 = 62 | /26 | `11000000` |
| Finance | 36 | 2⁶ − 2 = 62 | /26 | `11000000` |
| Human Resources | 30 | 2⁵ − 2 = 30 | /27 | `11100000` |
| IT Support | 30 | 2⁵ − 2 = 30 | /27 | `11100000` |
| Management | 20 | 2⁵ − 2 = 30 | /27 | `11100000` |

---

## Key design decisions

- **Star topology with managed switches**: centralised management for a small IT team, isolated faults, dedicated 1 Gbps per port, and simple to expand.
- **VLAN per department (802.1Q)**: VLAN IDs 10–60 map directly to the subnets, which keeps troubleshooting simple and isolates sensitive Finance and HR traffic.
- **Static + DHCP split**: low addresses in each subnet are reserved for gateways, printers, servers and APs; higher addresses form the DHCP pool for PCs and VoIP phones.
- **Security**: inter-VLAN ACLs and stricter firewall rules for the Finance and HR subnets, HTTPS-only access to sensitive systems.
- **QoS**: VoIP gets top priority (DSCP marking, about 128 kbps reserved per phone), followed by finance transactions, business apps, general data, then web browsing.

## Skills demonstrated

`IPv4 addressing` · `VLSM subnetting` · `VLAN design (802.1Q)` · `Network topology design` ·
`OSI / TCP-IP models` · `Core protocols (DHCP, DNS, SNMP, SSH, NTP, ARP…)` · `QoS` ·
`Network security planning` · `Technical report writing`

## Repository contents

```
.
├── README.md
├── report/
│   └── BlueWave-Network-Design-Report.pdf        # Full 64-page report
└── tables/
    └── Addressing-and-Subnetting-Tables.xlsx     # Requirement, addressing & calculation tables
```

---

**Author:** Thet Htoo Zin · HND in Computing, GUSTO College · December 2025

*This was an academic assignment. BlueWave Industries Ltd. and TechNova Network Solutions are part of the assignment case study.*
