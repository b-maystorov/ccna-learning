# Lab 04 – VLANs and Trunks

## Lab Goal

The goal of this lab is to configure two VLANs across two Cisco switches and connect both switches using an 802.1Q trunk.

The lab demonstrates:

- Creating VLANs
- Naming VLANs
- Configuring access ports
- Assigning switch ports to VLANs
- Configuring an 802.1Q trunk
- Restricting allowed VLANs on a trunk
- Verifying VLAN and trunk configuration
- Testing communication between devices in the same VLAN
- Testing isolation between different VLANs

---

# 1. Topology

Devices used:

- 2x Cisco 2960 switches
- 4x PCs

Topology:

```text
PC0                         PC2
 |                           |
 | Fa0/1                 Fa0/1 |
 |                           |
SW0 Gi0/1 ============== Gi0/1 SW1
 |                           |
 | Fa0/2                 Fa0/2 |
 |                           |
PC1                         PC3
```

The connection between the two switches is used as a trunk.

---

# 2. VLAN Design

Two VLANs are used:

| VLAN | Name |
|---|---|
| 10 | IT |
| 20 | HR |

Device membership:

```text
VLAN 10 – IT
PC0
PC2

VLAN 20 – HR
PC1
PC3
```

Even though PC0 and PC1 are connected to the same physical switch, they belong to different VLANs.

This means they are in different Layer 2 broadcast domains.

---

# 3. IP Addressing

## VLAN 10

Network:

```text
192.168.10.0/24
```

Addresses:

```text
PC0 = 192.168.10.10
PC2 = 192.168.10.20
```

Subnet mask:

```text
255.255.255.0
```

---

## VLAN 20

Network:

```text
192.168.20.0/24
```

Addresses:

```text
PC1 = 192.168.20.10
PC3 = 192.168.20.20
```

Subnet mask:

```text
255.255.255.0
```

No default gateway is configured because this lab does not use inter-VLAN routing.

---

# 4. Initial Connectivity Test

Before configuring VLANs, all switch ports are in VLAN 1 by default.

PC0 can communicate with PC2 because both use the same IP subnet:

```text
ping 192.168.10.20
```

However, PC0 cannot communicate with PC1 because they are in different IP networks and no router exists between them.

```text
ping 192.168.20.10
```

---

# 5. Create VLANs on Switch0

Enter privileged mode:

```text
enable
```

Enter global configuration mode:

```text
configure terminal
```

Create VLAN 10:

```text
vlan 10
name IT
exit
```

Create VLAN 20:

```text
vlan 20
name HR
exit
```

Return to privileged mode:

```text
end
```

Verify:

```text
show vlan brief
```

Expected VLANs:

```text
10   IT
20   HR
```

---

# 6. Configure Access Ports on Switch0

PC0 is connected to:

```text
FastEthernet0/1
```

Configure the port:

```text
configure terminal

interface fastEthernet 0/1
switchport mode access
switchport access vlan 10
exit
```

PC1 is connected to:

```text
FastEthernet0/2
```

Configure:

```text
interface fastEthernet 0/2
switchport mode access
switchport access vlan 20
end
```

Verify:

```text
show vlan brief
```

Expected result:

```text
VLAN 10 → Fa0/1
VLAN 20 → Fa0/2
```

---

# 7. Create VLANs on Switch1

The same VLANs must also exist on the second switch.

```text
enable
configure terminal

vlan 10
name IT
exit

vlan 20
name HR
exit
```

---

# 8. Configure Access Ports on Switch1

PC2 is connected to Fa0/1 and belongs to VLAN 10:

```text
interface fastEthernet 0/1
switchport mode access
switchport access vlan 10
exit
```

PC3 is connected to Fa0/2 and belongs to VLAN 20:

```text
interface fastEthernet 0/2
switchport mode access
switchport access vlan 20
end
```

Verify:

```text
show vlan brief
```

Expected:

```text
VLAN 10 → Fa0/1
VLAN 20 → Fa0/2
```

---

# 9. Configure the Trunk

The switches are connected using:

```text
GigabitEthernet0/1
```

The trunk allows multiple VLANs to travel across one physical link.

On Switch0:

```text
configure terminal

interface gigabitEthernet 0/1
switchport mode trunk
end
```

On Switch1:

```text
configure terminal

interface gigabitEthernet 0/1
switchport mode trunk
end
```

---

# 10. Verify the Trunk

Use:

```text
show interfaces trunk
```

Expected information:

```text
Port     Mode   Encapsulation   Status     Native vlan
Gi0/1    on     802.1q          trunking   1
```

The trunk uses:

```text
IEEE 802.1Q
```

802.1Q adds VLAN information to Ethernet frames while they travel across the trunk.

---

# 11. Restrict Allowed VLANs

By default, many VLANs may be allowed across the trunk.

In this lab only VLAN 10 and VLAN 20 are needed.

On both switches:

```text
configure terminal

interface gigabitEthernet 0/1
switchport trunk allowed vlan 10,20
end
```

Verify again:

```text
show interfaces trunk
```

Expected:

```text
Vlans allowed on trunk

Gi0/1   10,20
```

---

# 12. Same-VLAN Communication

VLAN 10 spans both switches.

PC0:

```text
192.168.10.10
```

PC2:

```text
192.168.10.20
```

Test from PC0:

```text
ping 192.168.10.20
```

Expected:

```text
Success
```

The Ethernet frames travel:

```text
PC0
 |
Fa0/1
 |
Switch0
 |
Gi0/1
 |
802.1Q Trunk
 |
Gi0/1
 |
Switch1
 |
Fa0/1
 |
PC2
```

---

# 13. VLAN 20 Communication

PC1:

```text
192.168.20.10
```

PC3:

```text
192.168.20.20
```

Test:

```text
ping 192.168.20.20
```

Expected:

```text
Success
```

VLAN 20 is also transported across the trunk.

---

# 14. Communication Between Different VLANs

PC0 is in:

```text
VLAN 10
192.168.10.10
```

PC1 is in:

```text
VLAN 20
192.168.20.10
```

Test:

```text
ping 192.168.20.10
```

Expected:

```text
Request timed out
```

The communication fails because VLAN 10 and VLAN 20 are separate Layer 2 networks.

A Layer 3 device is required for communication between them.

This is called:

```text
Inter-VLAN Routing
```

---

# 15. Access Ports

An access port normally belongs to one VLAN.

Example:

```text
interface fastEthernet 0/1
switchport mode access
switchport access vlan 10
```

This means:

```text
Fa0/1 = Access Port
Fa0/1 = VLAN 10
```

A PC connected to this port becomes part of VLAN 10.

---

# 16. Trunk Ports

A trunk port can carry traffic from multiple VLANs.

Example:

```text
interface gigabitEthernet 0/1
switchport mode trunk
```

In this lab the trunk carries:

```text
VLAN 10
VLAN 20
```

across the connection between the two switches.

---

# 17. 802.1Q

802.1Q is the VLAN tagging standard used on Ethernet trunk links.

Very simplified:

```text
Normal Ethernet frame:

[ Ethernet Frame ]
```

Across a trunk:

```text
[ VLAN Tag ][ Ethernet Frame ]
```

The VLAN ID allows the receiving switch to determine which VLAN the frame belongs to.

---

# 18. Native VLAN

The default native VLAN on many Cisco switches is:

```text
VLAN 1
```

Native VLAN traffic is normally sent untagged across an 802.1Q trunk.

The native VLAN should match on both ends of a trunk.

---

# 19. Important Verification Commands

Display VLANs:

```text
show vlan brief
```

This shows:

```text
VLAN ID
VLAN Name
Status
Assigned Access Ports
```

Display trunk information:

```text
show interfaces trunk
```

This shows:

```text
Trunk interface
802.1Q status
Native VLAN
Allowed VLANs
Active VLANs
```

Display the active configuration:

```text
show running-config
```

---

# 20. Interface Types Used

The Cisco 2960 switch in this lab has FastEthernet and GigabitEthernet interfaces.

FastEthernet examples:

```text
Fa0/1
Fa0/2
```

These were used for the PCs.

GigabitEthernet:

```text
Gi0/1
```

was used for the switch-to-switch trunk.

Typical speeds:

```text
FastEthernet = 100 Mbps
GigabitEthernet = 1000 Mbps / 1 Gbps
```

A trunk does not have to use GigabitEthernet.

The important rule is to configure the interface where the cable is actually connected.

---

# 21. Troubleshooting Example

During the lab, the allowed VLAN command was accidentally entered under:

```text
FastEthernet0/1
```

instead of:

```text
GigabitEthernet0/1
```

The command was valid, but it was applied to the wrong physical interface.

The incorrect configuration was removed:

```text
interface fastEthernet 0/1
no switchport trunk allowed vlan 10,20
```

The correct trunk interface was then configured:

```text
interface gigabitEthernet 0/1
switchport trunk allowed vlan 10,20
```

This demonstrates an important troubleshooting rule:

```text
Always verify which physical port the cable is connected to before configuring the interface.
```

---

# 22. Save the Configuration

On both switches:

```text
copy running-config startup-config
```

Press Enter when Cisco IOS asks for the destination filename.

Expected:

```text
Building configuration...
[OK]
```

---

# 23. Important Commands

| Command | Purpose |
|---|---|
| `vlan 10` | Create or configure VLAN 10 |
| `name IT` | Assign a name to a VLAN |
| `show vlan brief` | Display VLANs and access ports |
| `interface fastEthernet 0/1` | Configure Fa0/1 |
| `switchport mode access` | Configure an access port |
| `switchport access vlan 10` | Assign the port to VLAN 10 |
| `interface gigabitEthernet 0/1` | Configure Gi0/1 |
| `switchport mode trunk` | Configure a trunk port |
| `switchport trunk allowed vlan 10,20` | Allow only VLAN 10 and VLAN 20 |
| `show interfaces trunk` | Verify trunk configuration |
| `show running-config` | Display active configuration |
| `copy running-config startup-config` | Save the configuration |

---

# 24. Final Result

The final network contains two VLANs spanning two physical switches:

```text
                 VLAN 10
PC0 -------------------------------- PC2
 |                                    |
 |                                    |
SW0 ============= TRUNK ============= SW1
 |                                    |
 |                                    |
PC1 -------------------------------- PC3
                 VLAN 20
```

VLAN 10:

```text
PC0 ↔ PC2
Works
```

VLAN 20:

```text
PC1 ↔ PC3
Works
```

Between VLANs:

```text
PC0 ↔ PC1
Fails
```

This proves that:

- VLANs create separate broadcast domains
- Access ports assign end devices to VLANs
- Trunks transport multiple VLANs between switches
- 802.1Q identifies VLAN traffic across trunk links
- Devices in different VLANs require Layer 3 routing to communicate