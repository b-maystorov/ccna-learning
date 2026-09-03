# CCNA Learning & Packet Tracer Labs

This repository documents my practical learning path toward the **Cisco CCNA 200-301** certification.

The project combines networking theory with hands-on Cisco Packet Tracer labs. Each lab is documented so that it can be rebuilt from scratch without needing the completed Packet Tracer file.

---

## Purpose

The goal of this repository is to:

- learn CCNA networking fundamentals
- practice Cisco networking with Packet Tracer
- document every lab step by step
- practice troubleshooting and Cisco IOS commands
- build a practical networking portfolio
- create a reusable CCNA lab collection

---

## Learning Method

Each topic follows the same general process:

1. Learn the networking theory
2. Answer practice questions
3. Build a Cisco Packet Tracer lab
4. Test and troubleshoot the network
5. Inspect packets, tables, and protocols
6. Document the complete lab on GitHub

The labs become more complex as the course progresses.

---

## Repository Structure

The repository is organized by CCNA topic.

```text
ccna-learning/
│
├── README.md
│
├── 01-network-fundamentals/
│   └── labs/
│       ├── 01-first-lan.md
│       └── 01-first-lan.pkt
│
├── 02-ipv4-subnetting/
│   └── labs/
│
├── 03-cisco-ios/
│   └── labs/
│
└── ...
```

Each topic has its own directory.

Packet Tracer exercises are stored inside the `labs/` directory.

---

## Lab Documentation

Every lab normally contains two files.

### Markdown Lab Guide

Example:

```text
01-first-lan.md
```

The Markdown file contains the complete instructions for rebuilding the lab.

It includes:

- lab goal
- required devices
- network topology
- cabling instructions
- IP configuration
- Cisco IOS commands
- testing commands
- expected results
- troubleshooting steps
- protocol explanations
- important concepts learned during the lab

The intention is that another person can follow the `.md` file and reproduce the lab from zero.

### Packet Tracer File

Example:

```text
01-first-lan.pkt
```

The `.pkt` file contains the completed Cisco Packet Tracer network.

It can be opened to inspect the finished topology, configuration, and network behavior.

---

## How to Use the Labs

### Option 1 - Build the Lab Yourself

This is the recommended method.

1. Open the `.md` lab guide.
2. Start a new empty project in Cisco Packet Tracer.
3. Follow the instructions step by step.
4. Configure the network yourself.
5. Test the network using the commands provided in the guide.
6. Compare your result with the completed `.pkt` file if necessary.

---

### Option 2 - Inspect the Completed Lab

Open the corresponding `.pkt` file with Cisco Packet Tracer.

This can be useful for:

- checking the final topology
- inspecting device configurations
- troubleshooting your own version
- using Simulation Mode
- studying packet flow

---

## Requirements

To use the practical labs you need:

- Cisco Packet Tracer
- basic command-line knowledge
- Git and GitHub if you want to clone or modify the repository

Cisco Packet Tracer is available through Cisco Networking Academy / Skills for All.

---

## Clone the Repository

Using SSH:

```bash
git clone git@github.com:b-maystorov/ccna-learning.git
cd ccna-learning
```

Or clone it using the HTTPS option provided by GitHub.

---

## CCNA Learning Path

| Section | Topic | Status |
|---|---|---|
| 01 | Network Fundamentals | ✅ Completed |
| 02 | IPv4 & Subnetting | ⏳ In Progress |
| 03 | Cisco IOS Basics | Not started |
| 04 | VLANs & Trunks | Not started |
| 05 | STP & EtherChannel | Not started |
| 06 | Routing | Not started |
| 07 | OSPF | Not started |
| 08 | DHCP & NAT | Not started |
| 09 | ACLs & Network Security | Not started |
| 10 | IPv6 | Not started |
| 11 | Wireless & Network Services | Not started |
| 12 | Automation & Programmability | Not started |
| 13 | Troubleshooting | Not started |
| 14 | Final Labs & CCNA Review | Not started |

---

## Tools Used

The project mainly uses:

- Cisco Packet Tracer
- Cisco IOS CLI
- Linux
- Git
- GitHub

---

## Long-Term Goal

The repository will gradually grow from small LAN exercises into larger network environments containing technologies such as:

- IPv4 and IPv6
- subnetting
- VLANs
- 802.1Q trunks
- Spanning Tree Protocol
- EtherChannel
- static routing
- OSPF
- DHCP
- NAT
- ACLs
- wireless networking
- network security
- automation
- troubleshooting

The final goal is to be able to design, configure, verify, and troubleshoot complete networks at CCNA level.