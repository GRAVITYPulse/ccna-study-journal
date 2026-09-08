# Cisco CCNA Troubleshooting Commands

A practical reference of Cisco IOS/IOS-XE troubleshooting commands for CCNA-level networking.

---

## Table of Contents

* [1. Basic IOS Information](#1-basic-ios-information)
* [2. Interface & Layer 1 Troubleshooting](#2-interface--layer-1-troubleshooting)
* [3. Layer 2 Switching](#3-layer-2-switching)
* [4. VLAN Troubleshooting](#4-vlan-troubleshooting)
* [5. Trunking](#5-trunking)
* [6. STP Troubleshooting](#6-stp-troubleshooting)
* [7. EtherChannel](#7-etherchannel)
* [8. MAC Address Table](#8-mac-address-table)
* [9. Layer 3 & IP Troubleshooting](#9-layer-3--ip-troubleshooting)
* [10. ARP](#10-arp)
* [11. Routing Table](#11-routing-table)
* [12. Static Routing](#12-static-routing)
* [13. Default Route](#13-default-route)
* [14. RIP](#14-rip)
* [15. OSPF](#15-ospf)
* [16. DHCP](#16-dhcp)
* [17. DHCP Relay](#17-dhcp-relay)
* [18. NAT](#18-nat)
* [19. IPv6](#19-ipv6)
* [20. ACLs](#20-acls)
* [21. SSH & Remote Access](#21-ssh--remote-access)
* [22. CDP](#22-cdp)
* [23. LLDP](#23-lldp)
* [24. Port Security](#24-port-security)
* [25. HSRP](#25-hsrp)
* [26. DNS](#26-dns)
* [27. NTP](#27-ntp)
* [28. Syslog & Logging](#28-syslog--logging)
* [29. Interface Counters & Errors](#29-interface-counters--errors)
* [30. Connectivity Testing](#30-connectivity-testing)
* [31. Packet Path Troubleshooting](#31-packet-path-troubleshooting)
* [32. Configuration Troubleshooting](#32-configuration-troubleshooting)
* [33. Cisco IOS Debugging](#33-cisco-ios-debugging)
* [34. Password & Recovery Information](#34-password--recovery-information)
* [35. Useful Show Command Shortcuts](#35-useful-show-command-shortcuts)
* [36. CCNA Troubleshooting Workflow](#36-ccna-troubleshooting-workflow)

---

# 1. Basic IOS Information

## Display the running configuration

```cisco
show running-config
```

Displays the active configuration currently in RAM.

---

## Display the startup configuration

```cisco
show startup-config
```

Displays the configuration saved in NVRAM.

---

## Compare running and startup configuration

```cisco
show running-config
show startup-config
```

Useful for determining whether recent configuration changes have been saved.

---

## Display IOS version

```cisco
show version
```

Useful for checking:

* IOS/IOS-XE version
* Device model
* Uptime
* RAM
* Flash
* Configuration register
* Image information

---

## Display hardware information

```cisco
show inventory
```

Useful for identifying installed hardware and modules.

---

## Display flash contents

```cisco
show flash:
```

or:

```cisco
dir flash:
```

---

## Display filesystem information

```cisco
show file systems
```

---

## Display CPU utilization

```cisco
show processes cpu
```

More detailed:

```cisco
show processes cpu sorted
```

---

## Display memory utilization

```cisco
show memory
```

---

## Display device uptime

```cisco
show version | include uptime
```

---

# 2. Interface & Layer 1 Troubleshooting

The first question when troubleshooting connectivity should usually be:

> Is the interface physically and logically operational?

---

## Display all interfaces

```cisco
show ip interface brief
```

One of the most important CCNA troubleshooting commands.

Example:

```text
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0     192.168.1.1     YES manual up                    up
GigabitEthernet0/1     unassigned      YES unset  administratively down down
```

### Status / Protocol interpretation

| Status                | Protocol | Meaning               |
| --------------------- | -------- | --------------------- |
| up                    | up       | Operational           |
| down                  | down     | Physical/link problem |
| administratively down | down     | Interface is shutdown |
| up                    | down     | Layer 2 problem       |

---

## Display detailed interface information

```cisco
show interfaces
```

Specific interface:

```cisco
show interfaces gigabitEthernet 0/0
```

Check:

* Interface status
* Speed
* Duplex
* MTU
* Errors
* Drops
* CRC
* Collisions
* Input/output counters

---

## Display interface description

```cisco
show interfaces description
```

Very useful for quickly identifying what each interface connects to.

---

## Check interface configuration

```cisco
show running-config interface gigabitEthernet 0/0
```

---

## Check interface IP information

```cisco
show ip interface gigabitEthernet 0/0
```

---

## Check IPv6 interface information

```cisco
show ipv6 interface gigabitEthernet 0/0
```

---

## Enable an administratively down interface

```cisco
configure terminal
interface gigabitEthernet 0/0
no shutdown
```

---

## Check interface counters

```cisco
show interfaces counters
```

---

## Reset interface counters

```cisco
clear counters
```

Specific interface:

```cisco
clear counters gigabitEthernet 0/0
```

---

# 3. Layer 2 Switching

## Display VLANs

```cisco
show vlan brief
```

Verify:

* VLAN exists
* VLAN status
* Access ports assigned to VLAN

---

## Display detailed VLAN information

```cisco
show vlan
```

---

## Display VLAN configuration

```cisco
show running-config | section vlan
```

---

## Display switchport information

```cisco
show interfaces switchport
```

Specific interface:

```cisco
show interfaces gigabitEthernet 0/1 switchport
```

Check:

* Administrative mode
* Operational mode
* Access VLAN
* Native VLAN
* Voice VLAN
* Trunk status

---

# 4. VLAN Troubleshooting

### Verify VLAN exists

```cisco
show vlan brief
```

### Verify port membership

```cisco
show vlan brief
```

### Verify access VLAN

```cisco
show interfaces gigabitEthernet 0/1 switchport
```

### Verify VLAN configuration

```cisco
show running-config | section vlan
```

### Verify trunk carries VLAN

```cisco
show interfaces trunk
```

---

# 5. Trunking

## Display trunk interfaces

```cisco
show interfaces trunk
```

This is one of the primary trunk troubleshooting commands.

Check:

* Trunking status
* Native VLAN
* Allowed VLANs
* Active VLANs
* VLANs forwarding

---

## Detailed trunk information

```cisco
show interfaces gigabitEthernet 0/1 switchport
```

---

## Check DTP status

```cisco
show dtp interface gigabitEthernet 0/1
```

---

## Check trunk configuration

```cisco
show running-config interface gigabitEthernet 0/1
```

---

# 6. STP Troubleshooting

## Display spanning-tree status

```cisco
show spanning-tree
```

---

## Display STP for a specific VLAN

```cisco
show spanning-tree vlan 10
```

Check:

* Root bridge
* Root ID
* Root port
* Designated ports
* Blocking ports
* Forwarding ports
* Port cost
* Port priority

---

## Display STP summary

```cisco
show spanning-tree summary
```

---

## Display STP interface information

```cisco
show spanning-tree interface gigabitEthernet 0/1
```

Detailed:

```cisco
show spanning-tree interface gigabitEthernet 0/1 detail
```

---

## Display root bridge information

```cisco
show spanning-tree root
```

---

## Check STP mode

```cisco
show spanning-tree summary
```

---

## Troubleshooting STP

Check in this order:

```cisco
show spanning-tree
show spanning-tree root
show spanning-tree blockedports
show spanning-tree interface gigabitEthernet 0/1 detail
```

---

# 7. EtherChannel

## Display EtherChannel summary

```cisco
show etherchannel summary
```

Important indicators:

```text
(P) = Port-channel member
(S) = Layer 2
(R) = Layer 3
(D) = Down
(I) = Stand-alone
```

---

## Display EtherChannel detail

```cisco
show etherchannel detail
```

---

## Display EtherChannel port information

```cisco
show etherchannel port
```

---

## Display EtherChannel protocol

```cisco
show etherchannel protocol
```

---

## Check Port-Channel interface

```cisco
show interfaces port-channel 1
```

---

## Check member interface configuration

```cisco
show running-config interface gigabitEthernet 0/1
show running-config interface gigabitEthernet 0/2
```

---

# 8. MAC Address Table

## Display entire MAC address table

```cisco
show mac address-table
```

---

## Display dynamically learned MAC addresses

```cisco
show mac address-table dynamic
```

---

## Search for a specific MAC address

```cisco
show mac address-table address <MAC>
```

Example:

```cisco
show mac address-table address 0011.2233.4455
```

---

## Display MAC addresses learned on an interface

```cisco
show mac address-table interface gigabitEthernet 0/1
```

---

## Display MAC addresses for a VLAN

```cisco
show mac address-table vlan 10
```

---

## Clear dynamic MAC addresses

```cisco
clear mac address-table dynamic
```

---

# 9. Layer 3 & IP Troubleshooting

## Display IP interfaces

```cisco
show ip interface brief
```

---

## Display detailed IP interface information

```cisco
show ip interface
```

---

## Check a specific interface

```cisco
show ip interface gigabitEthernet 0/0
```

---

## Check IP configuration

```cisco
show running-config | section interface
```

---

## Check IP routing enabled

```cisco
show running-config | include ip routing
```

---

# 10. ARP

ARP maps:

```text
IPv4 address → MAC address
```

---

## Display ARP table

```cisco
show ip arp
```

---

## Display ARP for a specific IP

```cisco
show ip arp <IP-address>
```

Example:

```cisco
show ip arp 192.168.1.10
```

---

## Clear ARP cache

```cisco
clear ip arp
```

Specific entry:

```cisco
clear ip arp 192.168.1.10
```

---

# 11. Routing Table

## Display IPv4 routing table

```cisco
show ip route
```

---

## Display routes for a specific network

```cisco
show ip route 192.168.10.0
```

---

## Display routes learned from a protocol

```cisco
show ip route ospf
show ip route rip
```

---

## Display connected routes

```cisco
show ip route connected
```

---

## Display local routes

```cisco
show ip route local
```

---

## Display static routes

```cisco
show ip route static
```

---

## Display default route

```cisco
show ip route 0.0.0.0
```

---

## Find the routing decision for a destination

```cisco
show ip route <destination-IP>
```

Example:

```cisco
show ip route 10.10.10.10
```

This answers:

> "Which route will the router use to reach this destination?"

---

# 12. Static Routing

## Display configured static routes

```cisco
show running-config | include ^ip route
```

---

## Verify static routes in routing table

```cisco
show ip route static
```

---

## Check next-hop reachability

```cisco
ping <next-hop-IP>
```

---

## Check routing decision

```cisco
show ip route <destination-IP>
```

---

# 13. Default Route

## Check default route

```cisco
show ip route 0.0.0.0
```

or:

```cisco
show ip route | include Gateway
```

---

## Check configured default route

```cisco
show running-config | include ^ip route 0.0.0.0
```

---

# 14. RIP

## Display routing protocols

```cisco
show ip protocols
```

---

## Display RIP routes

```cisco
show ip route rip
```

---

## Display RIP configuration

```cisco
show running-config | section router rip
```

---

## Display RIP database

```cisco
show ip rip database
```

---

## Debug RIP

```cisco
debug ip rip
```

Disable debugging:

```cisco
undebug all
```

---

# 15. OSPF

## Display OSPF configuration

```cisco
show running-config | section router ospf
```

---

## Display OSPF process information

```cisco
show ip ospf
```

Check:

* Router ID
* Areas
* Timers
* SPF information

---

## Display OSPF interfaces

```cisco
show ip ospf interface
```

Specific interface:

```cisco
show ip ospf interface gigabitEthernet 0/0
```

---

## Display OSPF neighbors

```cisco
show ip ospf neighbor
```

This is one of the most important OSPF troubleshooting commands.

---

## Display OSPF routes

```cisco
show ip route ospf
```

---

## Display OSPF database

```cisco
show ip ospf database
```

---

## Display OSPF neighbor details

```cisco
show ip ospf neighbor detail
```

---

## Debug OSPF adjacency

```cisco
debug ip ospf adj
```

Debug OSPF packets:

```cisco
debug ip ospf packet
```

Disable debugging:

```cisco
undebug all
```

---

# 16. DHCP

## Display DHCP configuration

```cisco
show running-config | section ip dhcp
```

---

## Display DHCP bindings

```cisco
show ip dhcp binding
```

---

## Display DHCP pool statistics

```cisco
show ip dhcp pool
```

---

## Display DHCP conflicts

```cisco
show ip dhcp conflict
```

---

## Display DHCP server statistics

```cisco
show ip dhcp server statistics
```

---

## Clear DHCP bindings

```cisco
clear ip dhcp binding *
```

---

## Clear DHCP conflicts

```cisco
clear ip dhcp conflict *
```

---

# 17. DHCP Relay

DHCP relay is commonly configured with:

```cisco
ip helper-address <DHCP-server-IP>
```

## Check relay configuration

```cisco
show running-config interface gigabitEthernet 0/0
```

Look for:

```text
ip helper-address 192.168.100.10
```

---

## Check interface IP

```cisco
show ip interface gigabitEthernet 0/0
```

---

## Test DHCP server reachability

```cisco
ping <DHCP-server-IP>
```

---

## Debug DHCP relay/server operation

```cisco
debug ip dhcp server events
```

```cisco
debug ip dhcp server packet
```

Disable:

```cisco
undebug all
```

---

# 18. NAT

## Display NAT translations

```cisco
show ip nat translations
```

---

## Display detailed NAT translations

```cisco
show ip nat translations verbose
```

---

## Display NAT statistics

```cisco
show ip nat statistics
```

---

## Check NAT configuration

```cisco
show running-config | include ip nat
```

---

## Check inside/outside interfaces

```cisco
show running-config interface gigabitEthernet 0/0
```

Look for:

```cisco
ip nat inside
```

or:

```cisco
ip nat outside
```

---

## Clear NAT translations

```cisco
clear ip nat translation *
```

---

## Debug NAT

```cisco
debug ip nat
```

Disable:

```cisco
undebug all
```

---

# 19. IPv6

## Display IPv6 interfaces

```cisco
show ipv6 interface brief
```

---

## Display detailed IPv6 interface information

```cisco
show ipv6 interface
```

---

## Display IPv6 routing table

```cisco
show ipv6 route
```

---

## Display IPv6 neighbors

```cisco
show ipv6 neighbors
```

---

## Test IPv6 connectivity

```cisco
ping ipv6 <IPv6-address>
```

---

## Trace IPv6 path

```cisco
traceroute ipv6 <IPv6-address>
```

---

# 20. ACLs

## Display configured ACLs

```cisco
show access-lists
```

---

## Display IPv4 ACLs

```cisco
show ip access-lists
```

---

## Display a specific ACL

```cisco
show ip access-lists <ACL-name-or-number>
```

---

## Check ACL application

```cisco
show ip interface gigabitEthernet 0/0
```

Look for:

```text
Inbound access list is ...
Outgoing access list is ...
```

---

## Check ACL counters

```cisco
show ip access-lists
```

Packet counters next to ACEs help identify which entries are matching traffic.

---

## Check ACL configuration

```cisco
show running-config | section access-list
```

---

## IPv6 ACLs

```cisco
show ipv6 access-list
```

---

# 21. SSH & Remote Access

## Display SSH status

```cisco
show ip ssh
```

---

## Display active users

```cisco
show users
```

---

## Display VTY configuration

```cisco
show running-config | section line vty
```

---

## Display local usernames

```cisco
show running-config | include username
```

---

## Check VTY lines

```cisco
show line
```

---

## Test SSH from Cisco IOS

```cisco
ssh -l <username> <IP-address>
```

Example:

```cisco
ssh -l admin 192.168.1.1
```

---

# 22. CDP

## Display CDP status

```cisco
show cdp
```

---

## Display CDP neighbors

```cisco
show cdp neighbors
```

---

## Display detailed CDP information

```cisco
show cdp neighbors detail
```

---

## Display CDP information for an interface

```cisco
show cdp interface
```

---

# 23. LLDP

## Display LLDP status

```cisco
show lldp
```

---

## Display LLDP neighbors

```cisco
show lldp neighbors
```

---

## Display detailed LLDP information

```cisco
show lldp neighbors detail
```

---

# 24. Port Security

## Display port-security status

```cisco
show port-security
```

---

## Display port-security for an interface

```cisco
show port-security interface gigabitEthernet 0/1
```

---

## Display secure MAC addresses

```cisco
show port-security address
```

---

## Check interface status

```cisco
show interfaces status
```

---

## Check err-disabled ports

```cisco
show interfaces status err-disabled
```

---

## Check interface configuration

```cisco
show running-config interface gigabitEthernet 0/1
```

---

# 25. HSRP

## Display HSRP status

```cisco
show standby
```

---

## Display HSRP for a specific interface

```cisco
show standby gigabitEthernet 0/0
```

Check:

* Active router
* Standby router
* Virtual IP
* Priority
* Preemption
* State

---

## Display HSRP brief information

```cisco
show standby brief
```

---

# 26. DNS

## Display DNS configuration

```cisco
show hosts
```

---

## Display DNS-related configuration

```cisco
show running-config | include ip name-server
```

---

## Test DNS resolution

```cisco
ping <hostname>
```

Example:

```cisco
ping server.example.com
```

---

# 27. NTP

## Display NTP status

```cisco
show ntp status
```

---

## Display NTP associations

```cisco
show ntp associations
```

---

## Display NTP configuration

```cisco
show running-config | include ntp
```

---

# 28. Syslog & Logging

## Display system logs

```cisco
show logging
```

---

## Display logging configuration

```cisco
show running-config | include logging
```

---

## Check logging buffer

```cisco
show logging | include Buffer
```

---

## Clear logging buffer

```cisco
clear logging
```

---

# 29. Interface Counters & Errors

Use:

```cisco
show interfaces
```

Important counters include:

| Counter         | Possible indication                                   |
| --------------- | ----------------------------------------------------- |
| CRC             | Physical/media problems                               |
| input errors    | Layer 1/2 problems                                    |
| output errors   | Interface/hardware problems                           |
| collisions      | Duplex problems                                       |
| late collisions | Duplex/physical problems                              |
| overruns        | Interface cannot process incoming traffic fast enough |
| ignored         | Buffer/resource problems                              |
| drops           | Congestion/resource issues                            |
| runts           | Frames smaller than minimum Ethernet size             |
| giants          | Frames larger than supported MTU                      |

---

## Check interface errors

```cisco
show interfaces counters errors
```

---

## Check interface status

```cisco
show interfaces status
```

---

# 30. Connectivity Testing

## Ping

```cisco
ping <destination-IP>
```

Example:

```cisco
ping 192.168.1.1
```

---

## Extended ping

```cisco
ping
```

Cisco IOS interactively asks for:

* Protocol
* Destination
* Repeat count
* Datagram size
* Timeout
* Source interface/IP
* DF bit
* Data pattern

Useful for testing connectivity from a specific source.

---

## Traceroute

```cisco
traceroute <destination-IP>
```

Example:

```cisco
traceroute 8.8.8.8
```

---

## Extended traceroute

```cisco
traceroute
```

Allows selection of the source interface/IP and other parameters.

---

# 31. Packet Path Troubleshooting

When a host cannot reach another network:

```text
PC
 ↓
Access Switch
 ↓
Default Gateway
 ↓
Routing
 ↓
Next-Hop Router
 ↓
Destination Network
```

Check each stage.

### Step 1 — Interface

```cisco
show ip interface brief
```

### Step 2 — VLAN

```cisco
show vlan brief
```

### Step 3 — Trunk

```cisco
show interfaces trunk
```

### Step 4 — MAC address

```cisco
show mac address-table
```

### Step 5 — ARP

```cisco
show ip arp
```

### Step 6 — Routing table

```cisco
show ip route
```

### Step 7 — Next hop

```cisco
ping <next-hop>
```

### Step 8 — Destination

```cisco
ping <destination>
```

### Step 9 — Path

```cisco
traceroute <destination>
```

---

# 32. Configuration Troubleshooting

## Search running configuration

```cisco
show running-config | include <keyword>
```

Example:

```cisco
show running-config | include ospf
```

---

## Search using section

```cisco
show running-config | section router ospf
```

---

## Search interfaces

```cisco
show running-config | section interface
```

---

## Search ACLs

```cisco
show running-config | include access-list
```

---

## Search IP routes

```cisco
show running-config | include ip route
```

---

## Search DHCP configuration

```cisco
show running-config | section ip dhcp
```

---

## Search NAT configuration

```cisco
show running-config | include ip nat
```

---

## Search hostname

```cisco
show running-config | include hostname
```

---

# 33. Cisco IOS Debugging

`debug` commands provide real-time information about protocol operation.

**Warning:** Debugging can consume CPU and should be used carefully on production devices.

---

## Check active debugging

```cisco
show debugging
```

---

## Disable all debugging

```cisco
undebug all
```

or:

```cisco
no debug all
```

---

## Debug IP packets

```cisco
debug ip packet
```

Use with caution.

---

## Debug ICMP

```cisco
debug ip icmp
```

---

## Debug ARP

```cisco
debug arp
```

---

## Debug DHCP

```cisco
debug ip dhcp server events
```

```cisco
debug ip dhcp server packet
```

---

## Debug NAT

```cisco
debug ip nat
```

---

## Debug RIP

```cisco
debug ip rip
```

---

## Debug OSPF adjacency

```cisco
debug ip ospf adj
```

---

## Debug OSPF packets

```cisco
debug ip ospf packet
```

---

## Debug IPv6

```cisco
debug ipv6 packet
```

---

## Debug STP

```cisco
debug spanning-tree events
```

---

## Debug EthernetChannel

```cisco
debug etherchannel
```

---

# 34. Password & Recovery Information

## Check configuration register

```cisco
show version | include Configuration register
```

Typical value:

```text
0x2102
```

The configuration register affects how the router boots and loads its configuration.

---

# 35. Useful Show Command Shortcuts

Cisco IOS supports command abbreviations when enough characters uniquely identify the command.

Example:

```cisco
show running-config
```

can commonly be abbreviated as:

```cisco
sh run
```

---

## Useful shortcuts

```cisco
sh ip int br
```

```cisco
sh ip route
```

```cisco
sh ip ospf nei
```

```cisco
sh vlan br
```

```cisco
sh int status
```

```cisco
sh int trunk
```

```cisco
sh mac address-table
```

```cisco
sh cdp nei
```

```cisco
sh lldp nei
```

```cisco
sh etherchannel sum
```

---

## Output filtering

### Include

```cisco
show running-config | include ospf
```

Displays lines containing the specified keyword.

---

### Exclude

```cisco
show running-config | exclude !
```

Removes lines containing the specified keyword.

---

### Begin

```cisco
show running-config | begin interface
```

Starts displaying output at the first matching line.

---

### Section

```cisco
show running-config | section router ospf
```

Displays an entire configuration section.

---

# 36. CCNA Troubleshooting Workflow

A structured troubleshooting process is more effective than randomly entering commands.

## Step 1 — Identify the symptom

Determine exactly what is failing.

Examples:

```text
Host cannot ping gateway
Host can ping gateway but not remote network
OSPF neighbor is missing
VLAN hosts cannot communicate
Trunk is not carrying VLAN
EtherChannel is down
DHCP client receives no address
```

---

## Step 2 — Check Layer 1

```cisco
show interfaces status
show ip interface brief
show interfaces
```

Check for:

```text
administratively down
down/down
CRC errors
input errors
output errors
duplex problems
```

---

## Step 3 — Check Layer 2

```cisco
show vlan brief
show interfaces switchport
show interfaces trunk
show spanning-tree
show etherchannel summary
show mac address-table
```

---

## Step 4 — Check Layer 3

```cisco
show ip interface brief
show ip arp
show ip route
```

---

## Step 5 — Test connectivity

```cisco
ping <gateway>
ping <next-hop>
ping <destination>
```

Then:

```cisco
traceroute <destination>
```

---

## Step 6 — Check the relevant protocol

### OSPF

```cisco
show ip ospf neighbor
show ip ospf interface
show ip route ospf
```

### RIP

```cisco
show ip protocols
show ip rip database
show ip route rip
```

### DHCP

```cisco
show ip dhcp binding
show ip dhcp pool
show ip dhcp conflict
```

### NAT

```cisco
show ip nat translations
show ip nat statistics
```

### STP

```cisco
show spanning-tree
```

### EtherChannel

```cisco
show etherchannel summary
```

---

## Step 7 — Inspect configuration

```cisco
show running-config
```

Or narrow the search:

```cisco
show running-config | section <section>
```

---

## Step 8 — Use debugging only when necessary

```cisco
show debugging
```

Then enable the required debug.

Example:

```cisco
debug ip ospf adj
```

After troubleshooting:

```cisco
undebug all
```

---

# Quick Reference — Most Important CCNA Commands

If only a handful of commands can be remembered, these should be prioritized:

```cisco
show running-config
show startup-config
show version

show ip interface brief
show interfaces
show interfaces status
show interfaces description

show vlan brief
show interfaces switchport
show interfaces trunk

show spanning-tree
show spanning-tree vlan <vlan-id>

show etherchannel summary

show mac address-table
show ip arp

show ip route
show ip route <destination>

show ip protocols

show ip ospf
show ip ospf neighbor
show ip ospf interface
show ip ospf database

show ip dhcp binding
show ip dhcp pool

show ip nat translations
show ip nat statistics

show access-lists
show ip access-lists

show cdp neighbors
show cdp neighbors detail

show lldp neighbors
show lldp neighbors detail

show port-security interface <interface>

show standby

ping <destination>
traceroute <destination>

show logging
show debugging
undebug all
```

---

# Golden Troubleshooting Sequence

For a general connectivity problem, this sequence is a strong starting point:

```cisco
show ip interface brief
show interfaces status
show vlan brief
show interfaces trunk
show spanning-tree
show etherchannel summary
show mac address-table
show ip arp
show ip route
ping <destination>
traceroute <destination>
show running-config
```

The fundamental troubleshooting model is:

```text
Layer 1
  ↓
Interface / Physical
  ↓
Layer 2
  ↓
VLAN / Trunk / STP / EtherChannel / MAC
  ↓
Layer 3
  ↓
IP / ARP / Routing
  ↓
Routing Protocol
  ↓
Services
  ↓
ACL / NAT / DHCP / DNS
  ↓
Application
```

> **Troubleshoot from the bottom up.**
>
> If Layer 1 is broken, there is little value in troubleshooting OSPF.
> If Layer 2 is broken, investigate VLANs/trunks/STP before blaming routing.
> If Layer 3 is broken, verify IP addressing, ARP, and the routing table before investigating higher-level services.
