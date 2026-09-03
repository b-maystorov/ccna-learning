# Lab 01 - First LAN

## Goal

Build a simple LAN with two PCs connected through a Cisco switch.

At the end of this lab we will:

- connect two PCs through a switch
- configure static IPv4 addresses
- test connectivity with ping
- inspect the ARP table
- inspect the switch MAC address table
- use Packet Tracer Simulation Mode
- inspect ICMP and ARP traffic

---

## Topology

```text
PC0 -------- Switch0 -------- PC1

Devices:

2x PC-PT
1x Cisco 2960-24TT switch
Step 1 - Add the Devices

Open Cisco Packet Tracer.

Add the PCs

At the bottom-left:

Click End Devices
Select PC
Drag two PCs into the workspace

Packet Tracer should name them:

PC0
PC1
Add the Switch

At the bottom-left:

Click Network Devices
Click Switches
Select 2960-24TT
Drag the switch between the two PCs

The topology should now look like:

PC0       Switch0       PC1
Step 2 - Connect the Devices

At the bottom-left, click the Connections lightning icon.

Select:

Copper Straight-Through

Connect PC0:

PC0 FastEthernet0
        |
        |
Switch0 FastEthernet0/1

Connect PC1:

PC1 FastEthernet0
        |
        |
Switch0 FastEthernet0/2

The final topology should look like:

PC0 ---- Fa0/1 Switch0 Fa0/2 ---- PC1

Wait a few seconds until the connection indicators become green.

Step 3 - Configure PC0

Click:

PC0

Then open:

Desktop
→ IP Configuration

Select Static.

Enter:

IP Address:      192.168.1.10
Subnet Mask:     255.255.255.0
Default Gateway: leave empty

No default gateway is required because both PCs are in the same local network.

Step 4 - Configure PC1

Click:

PC1

Then open:

Desktop
→ IP Configuration

Select Static.

Enter:

IP Address:      192.168.1.20
Subnet Mask:     255.255.255.0
Default Gateway: leave empty
Step 5 - Test Connectivity

Open PC0:

PC0
→ Desktop
→ Command Prompt

Run:

ping 192.168.1.20

A successful result should show replies from:

192.168.1.20

Example:

Packets: Sent = 4, Received = 4, Lost = 0

Ping uses:

ICMP - Internet Control Message Protocol

ICMP Echo Requests are sent from PC0 and PC1 responds with ICMP Echo Replies.

Step 6 - Inspect the ARP Table

Still inside the PC0 Command Prompt, run:

arp -a

An entry for PC1 should appear.

Example:

Internet Address    Physical Address
192.168.1.20        00e0.8f5b.9268

ARP means:

ARP - Address Resolution Protocol

ARP maps:

IPv4 address → MAC address

The MAC address can be different in another Packet Tracer lab because Packet Tracer generates MAC addresses automatically.

Step 7 - Inspect the Switch MAC Address Table

Click:

Switch0

Open:

CLI

Press Enter if Packet Tracer asks you to start the CLI.

Enter:

enable

Then:

show mac address-table

The switch should show entries similar to:

Mac Address       Type       Ports

xxxx.xxxx.xxxx    DYNAMIC    Fa0/1
xxxx.xxxx.xxxx    DYNAMIC    Fa0/2

The switch has learned which MAC address is connected to each port.

Important rule:

Source MAC      → used for learning
Destination MAC → used for forwarding

The switch MAC address table maps:

MAC address → switch port
Step 8 - Open Simulation Mode

At the bottom-right of Packet Tracer, click:

Simulation

Now return to:

PC0
→ Desktop
→ Command Prompt

Run:

ping 192.168.1.20

Packet Tracer will show packets moving between the devices:

PC0 → Switch0 → PC1
PC0 ← Switch0 ← PC1

The ping command normally sends multiple ICMP Echo Requests.

For every Echo Request, PC1 sends back an Echo Reply.

Step 9 - Inspect an ICMP Packet

In the Simulation Panel, click one of the ICMP events.

Open:

OSI Model

At Layer 3 you should see information such as:

Source IP:      192.168.1.10
Destination IP: 192.168.1.20
ICMP Type:      8

ICMP Type 8 means:

Echo Request

At Layer 2 you can see:

Source MAC
Destination MAC

This shows how the IP packet is encapsulated inside an Ethernet frame.

Step 10 - Observe ARP

When a PC knows an IPv4 address but does not know the corresponding MAC address, it uses ARP.

A normal ARP request is basically:

Who has 192.168.1.20?

The Ethernet destination MAC address of an ARP broadcast is:

FFFF.FFFF.FFFF

This is the Ethernet broadcast MAC address.

It means:

Send this frame to every device in the local broadcast domain.

The device that owns the requested IPv4 address sends an ARP Reply containing its MAC address.

Gratuitous ARP

During the lab, changing the IPv4 address of PC1 caused Packet Tracer to generate a Gratuitous ARP.

A Gratuitous ARP is used to announce an IP address to the local network.

It can help:

detect duplicate IP addresses
announce a new IP-to-MAC mapping
update ARP information on other devices

Simple difference:

Normal ARP:
Who has this IP?

Gratuitous ARP:
I am using this IP.
Important Concepts
ARP Table

Stored on hosts.

IPv4 address → MAC address
Switch MAC Address Table

Stored on the switch.

MAC address → switch port
Ping

Uses:

ICMP - Internet Control Message Protocol

to test IP connectivity.

Broadcast MAC
FFFF.FFFF.FFFF

means:

every device in the local broadcast domain
Final Result

The completed network should look like:

192.168.1.10                           192.168.1.20
     PC0 -------- 2960 Switch -------- PC1
                  Fa0/1  Fa0/2

PC0 and PC1 should successfully communicate using:

ping 192.168.1.20

The completed Packet Tracer file is:

01-first-lan.pkt