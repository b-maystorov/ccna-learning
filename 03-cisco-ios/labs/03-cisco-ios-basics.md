# Lab 03 – Cisco IOS Basics & SSH Remote Access

## Lab Goal

In this lab, a Cisco router is configured from scratch and then managed remotely from a PC using SSH.

The lab covers:

- Cisco IOS configuration modes
- Hostname configuration
- Interface configuration
- Console password protection
- `enable secret`
- Password encryption
- MOTD banner
- Local user accounts
- RSA key generation
- SSH version 2
- VTY line configuration
- Remote SSH login
- Configuration verification
- Saving the running configuration

---

# 1. Topology

Devices used:

- 1x Cisco 1941 Router
- 1x Cisco 2960-24TT Switch
- 1x PC

Topology:

```text
PC0 -------- Switch0 -------- R1
```

Connections:

```text
PC0 FastEthernet0
        |
        |
Switch0 FastEthernet0/1

Switch0 FastEthernet0/2
        |
        |
R1 GigabitEthernet0/0
```

Both connections use Copper Straight-Through cables.

---

# 2. IP Addressing

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| PC0 | FastEthernet0 | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 |
| R1 | GigabitEthernet0/0 | 192.168.1.1 | 255.255.255.0 | - |

Network:

```text
192.168.1.0/24
```

---

# 3. Initial Router Setup

When the router starts, Cisco IOS may ask:

```text
Would you like to enter the initial configuration dialog? [yes/no]:
```

Enter:

```text
no
```

The router can then be configured manually through the CLI.

---

# 4. Enter Privileged EXEC Mode

The router initially starts in User EXEC mode:

```text
Router>
```

Enter:

```text
enable
```

The prompt changes to:

```text
Router#
```

This is Privileged EXEC mode.

---

# 5. Enter Global Configuration Mode

```text
configure terminal
```

Prompt:

```text
Router(config)#
```

Global configuration mode is used to change the active device configuration.

---

# 6. Configure the Hostname

```text
hostname R1
```

The prompt changes from:

```text
Router(config)#
```

to:

```text
R1(config)#
```

A hostname makes network devices easier to identify.

---

# 7. Configure the Router Interface

Enter the interface:

```text
interface gigabitEthernet 0/0
```

Configure the interface:

```text
ip address 192.168.1.1 255.255.255.0
description LAN-to-Switch0
no shutdown
```

`no shutdown` enables the interface.

Without it, router interfaces are usually:

```text
administratively down
```

Return to Privileged EXEC mode:

```text
end
```

---

# 8. Verify the Interface

Use:

```text
show ip interface brief
```

Expected result:

```text
Interface              IP-Address      Status      Protocol
GigabitEthernet0/0     192.168.1.1    up          up
```

`up/up` means both the physical connection and the line protocol are working.

---

# 9. Configure PC0

On PC0:

```text
Desktop
→ IP Configuration
```

Configure:

```text
IP Address:      192.168.1.10
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.1.1
```

---

# 10. Test Connectivity

On PC0:

```text
Desktop
→ Command Prompt
```

Run:

```text
ping 192.168.1.1
```

Expected result:

```text
Sent = 4
Received = 4
Lost = 0
```

This confirms that PC0 can reach the router.

---

# 11. Configure an Enable Secret

Enter global configuration mode:

```text
configure terminal
```

Configure:

```text
enable secret Admin123
```

This protects access from:

```text
R1>
```

to:

```text
R1#
```

The password used here is only for this Packet Tracer lab.

---

# 12. Enable Password Encryption

```text
service password-encryption
```

This prevents many simple passwords from appearing directly in clear text inside the configuration.

It is mainly an obfuscation mechanism and should not be treated as strong encryption.

---

# 13. Configure an MOTD Banner

```text
banner motd #Authorized access only#
```

MOTD stands for:

```text
Message Of The Day
```

The `#` characters are used as delimiters around the message.

---

# 14. Configure Console Access

Select the console line:

```text
line console 0
```

Set the password:

```text
password Console123
```

Enable password checking:

```text
login
```

Complete console configuration:

```text
line console 0
password Console123
login
```

Return to Privileged EXEC mode:

```text
end
```

---

# 15. Create a Local User

Enter global configuration mode:

```text
configure terminal
```

Create the user:

```text
username Admin secret Bili123
```

This creates the local account used later for SSH authentication.

Lab credentials:

```text
Username: Admin
Password: Bili123
```

---

# 16. Configure the Domain Name

SSH key generation requires a hostname and domain name.

The hostname is already:

```text
R1
```

Configure the domain:

```text
ip domain-name lab.local
```

---

# 17. Generate RSA Keys

Generate the RSA key pair:

```text
crypto key generate rsa
```

Cisco IOS asks for the modulus size:

```text
How many bits in the modulus [512]:
```

For this Packet Tracer lab:

```text
1024
```

was used.

---

# 18. Enable SSH Version 2

```text
ip ssh version 2
```

SSH stands for:

```text
Secure Shell
```

SSH provides encrypted remote command-line access.

It is preferred over Telnet because Telnet transmits data in clear text.

---

# 19. Configure the VTY Lines

VTY stands for:

```text
Virtual Teletype
```

VTY lines are used for remote CLI sessions.

Select the first five VTY lines:

```text
line vty 0 4
```

Use the local user database:

```text
login local
```

Allow SSH only:

```text
transport input ssh
```

Complete VTY configuration:

```text
line vty 0 4
login local
transport input ssh
```

---

# 20. Test SSH from PC0

First verify connectivity again:

```text
ping 192.168.1.1
```

Then start the SSH session:

```text
ssh -l Admin 192.168.1.1
```

Enter the local user password when prompted.

After successful login, the router displays the banner:

```text
Authorized access only
```

and the prompt:

```text
R1>
```

This confirms that PC0 is remotely connected to R1 using SSH.

---

# 21. Enter Privileged EXEC Mode over SSH

From the SSH session:

```text
enable
```

Enter the enable secret.

After successful authentication:

```text
R1#
```

The router can now be fully administered remotely.

---

# 22. Verify Logged-In Users

Run:

```text
show users
```

The active SSH session appears on a VTY line.

In this lab, the user was:

```text
Admin
```

---

# 23. Verify SSH Status

Run:

```text
show ip ssh
```

The router showed:

```text
SSH Enabled - version 2.0
Authentication timeout: 120 secs
Authentication retries: 3
```

This confirms that SSH version 2 is active.

---

# 24. Verify the Running Configuration

Run:

```text
show running-config
```

Important configuration sections include:

```text
hostname R1
```

```text
enable secret ...
```

```text
username Admin secret ...
```

```text
ip ssh version 2
ip domain-name lab.local
```

Router interface:

```text
interface GigabitEthernet0/0
 description LAN-to-Switch0
 ip address 192.168.1.1 255.255.255.0
```

Console configuration:

```text
line console 0
 password ...
 login
```

VTY configuration:

```text
line vty 0 4
 login local
 transport input ssh
```

---

# 25. Running-Config vs Startup-Config

## Running-Config

The running configuration is the configuration currently active in RAM.

Display it with:

```text
show running-config
```

Changes take effect immediately.

However, they are lost after a restart unless they are saved.

## Startup-Config

The startup configuration is stored and loaded during device boot.

Display it with:

```text
show startup-config
```

---

# 26. Save the Configuration

Save the running configuration:

```text
copy running-config startup-config
```

Cisco IOS asks:

```text
Destination filename [startup-config]?
```

Press Enter.

Expected output:

```text
Building configuration...
[OK]
```

The configuration is now stored permanently.

---

# 27. Important Cisco IOS Modes

## User EXEC Mode

```text
R1>
```

Basic access with limited commands.

## Privileged EXEC Mode

```text
R1#
```

Administrative access.

Enter with:

```text
enable
```

## Global Configuration Mode

```text
R1(config)#
```

Enter with:

```text
configure terminal
```

## Interface Configuration Mode

```text
R1(config-if)#
```

Example:

```text
interface gigabitEthernet 0/0
```

## Line Configuration Mode

```text
R1(config-line)#
```

Used for console and VTY configuration.

Examples:

```text
line console 0
```

```text
line vty 0 4
```

---

# 28. IOS Navigation

Go back one configuration level:

```text
exit
```

Return directly to Privileged EXEC mode:

```text
end
```

---

# 29. Important Commands

| Command | Purpose |
|---|---|
| `enable` | Enter Privileged EXEC mode |
| `configure terminal` | Enter Global Configuration mode |
| `hostname R1` | Configure the device hostname |
| `interface gigabitEthernet 0/0` | Enter interface configuration mode |
| `ip address ...` | Configure an IPv4 address |
| `no shutdown` | Enable an interface |
| `description ...` | Add an interface description |
| `enable secret ...` | Protect Privileged EXEC access |
| `service password-encryption` | Obfuscate simple passwords in the config |
| `banner motd ...` | Configure a login banner |
| `line console 0` | Configure the console line |
| `password ...` | Configure a line password |
| `login` | Enable line password checking |
| `username ... secret ...` | Create a local user |
| `ip domain-name ...` | Configure the domain name |
| `crypto key generate rsa` | Generate RSA keys |
| `ip ssh version 2` | Enable SSH version 2 |
| `line vty 0 4` | Configure remote access lines |
| `login local` | Use local users for authentication |
| `transport input ssh` | Allow SSH only |
| `show users` | Display logged-in users |
| `show ip ssh` | Display SSH status |
| `show ip interface brief` | Display interface summary |
| `show running-config` | Display active configuration |
| `show startup-config` | Display saved configuration |
| `copy running-config startup-config` | Save the configuration |

---

# 30. Result

The lab was completed successfully.

PC0 can reach R1 over the local network and remotely manage the router using SSH.

Final topology:

```text
PC0
192.168.1.10
     |
     |
Switch0
     |
     |
R1 G0/0
192.168.1.1
```

Remote management path:

```text
PC0
 |
 | SSH
 v
R1
```

This lab demonstrates basic Cisco IOS configuration, access protection, SSH configuration, remote administration, verification, and configuration persistence.