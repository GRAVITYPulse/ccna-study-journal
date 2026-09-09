# Production Cisco IOS Configuration Plan & Scripts

**Scope:** Static IP, VLANs, Trunking, Router-on-a-Stick, Rapid-PVST+, OSPF, NAT/PAT, SSH, Switch Security

**Target Platform:** Cisco IOS / IOS-XE / Cisco Packet Tracer Topology

---

## Part A: Complete Addressing Table

### Router-to-Router / WAN Links

| Connection | Device | Interface | IP Address | Subnet Mask | Wildcard / CIDR |
| --- | --- | --- | --- | --- | --- |
| **ISP ↔ EDGE** | R-ISP | G0/0 | `203.0.113.1` | `255.255.255.252` | `/30` |
| **ISP ↔ EDGE** | R-EDGE | G0/2 | `203.0.113.2` | `255.255.255.252` | `/30` |
| **EDGE ↔ CORE1** | R-EDGE | G0/1 | `10.0.0.1` | `255.255.255.252` | `/30` |
| **EDGE ↔ CORE1** | R-CORE1 | G0/1 | `10.0.0.2` | `255.255.255.252` | `/30` |
| **EDGE ↔ CORE2** | R-EDGE | G0/0 | `10.0.0.5` | `255.255.255.252` | `/30` |
| **EDGE ↔ CORE2** | R-CORE2 | G0/0 | `10.0.0.6` | `255.255.255.252` | `/30` |
| **CORE1 ↔ CORE2** | R-CORE1 | G0/0 | `10.0.0.9` | `255.255.255.252` | `/30` |
| **CORE1 ↔ CORE2** | R-CORE2 | G0/1 | `10.0.0.10` | `255.255.255.252` | `/30` |

### Loopback Interfaces

| Device | Interface | IP Address | Subnet Mask | Purpose |
| --- | --- | --- | --- | --- |
| **R-EDGE** | Loopback0 | `1.1.1.1` | `255.255.255.255` | OSPF Router ID |
| **R-CORE1** | Loopback0 | `2.2.2.2` | `255.255.255.255` | OSPF Router ID |
| **R-CORE2** | Loopback0 | `3.3.3.3` | `255.255.255.255` | OSPF Router ID |
| **R-ISP** | Loopback0 | `8.8.8.8` | `255.255.255.255` | Simulated Internet Target |

### VLAN Networks & Gateways (R-CORE2)

| VLAN ID | VLAN Name | Network Address | Subnet Mask | Default Gateway (SVI / Subinterface) |
| --- | --- | --- | --- | --- |
| **10** | `SALES` | `192.168.10.0/24` | `255.255.255.0` | `192.168.10.1` (R-CORE2 G0/2.10) |
| **20** | `IT-ADMIN` | `172.16.20.0/24` | `255.255.255.0` | `172.16.20.1` (R-CORE2 G0/2.20) |
| **93** | `MANAGEMENT` | `172.20.0.0/28` | `255.255.255.240` | `172.20.0.1` (R-CORE2 G0/2.93) |

### Switch Management SVIs

| Switch | Management VLAN | SVI IP Address | Subnet Mask | Default Gateway |
| --- | --- | --- | --- | --- |
| **SW-DIST** | VLAN 93 | `172.20.0.2` | `255.255.255.240` | `172.20.0.1` |
| **SW-ACCESS1** | VLAN 93 | `172.20.0.3` | `255.255.255.240` | `172.20.0.1` |
| **SW-ACCESS2** | VLAN 93 | `172.20.0.4` | `255.255.255.240` | `172.20.0.1` |

---

## Part B: Complete Port / VLAN Table

| Device | Interface | Connected Device | Port Mode | VLAN / Allowed VLANs |
| --- | --- | --- | --- | --- |
| **R-CORE2** | G0/2 | SW-DIST G0/1 | Trunk (802.1Q Subinterfaces) | `10, 20, 93` |
| **SW-DIST** | G0/1 | R-CORE2 G0/2 | Trunk | `10, 20, 93` |
| **SW-DIST** | Fa0/1 | SW-ACCESS1 Fa0/1 | Trunk | `10, 20, 93` |
| **SW-DIST** | Fa0/2 | SW-ACCESS2 Fa0/2 | Trunk | `10, 20, 93` |
| **SW-ACCESS1** | Fa0/1 | SW-DIST Fa0/1 | Trunk | `10, 20, 93` |
| **SW-ACCESS1** | Fa0/3 | SW-ACCESS2 Fa0/3 | Trunk | `10, 20, 93` |
| **SW-ACCESS1** | Fa0/21 | PC0 | Access | `VLAN 10` |
| **SW-ACCESS1** | Fa0/22 | PC1 | Access | `VLAN 10` |
| **SW-ACCESS1** | Fa0/23 | PC2 | Access | `VLAN 20` |
| **SW-ACCESS1** | Fa0/24 | PC3 | Access | `VLAN 20` |
| **SW-ACCESS2** | Fa0/2 | SW-DIST Fa0/2 | Trunk | `10, 20, 93` |
| **SW-ACCESS2** | Fa0/3 | SW-ACCESS1 Fa0/3 | Trunk | `10, 20, 93` |
| **SW-ACCESS2** | Fa0/11 | PC5 | Access | `VLAN 93` |
| **SW-ACCESS2** | Fa0/12 | PC4 | Access | `VLAN 93` |

---

## Part C: Full Cisco IOS Configuration — R-ISP

```cisconetworking
enable
configure terminal

! --- Basic Setup & Security ---
hostname R-ISP
no ip domain-lookup
ip domain-name netadmin.local

! --- Management SSH Credentials ---
username admin privilege 15 secret AdminPass123!
crypto key generate rsa general-keys modulus 2048
ip ssh version 2

! --- Interfaces ---
interface Loopback0
 description Simulated Internet Destination
 ip address 8.8.8.8 255.255.255.255
 no shutdown

interface GigabitEthernet0/0
 description Link to R-EDGE (WAN)
 ip address 203.0.113.1 255.255.255.252
 no shutdown

! --- Console & VTY Securing ---
line con 0
 logging synchronous
 login local
line vty 0 4
 transport input ssh
 login local

end
write memory

```

---

## Part D: Full Cisco IOS Configuration — R-EDGE

```cisconetworking
enable
configure terminal

! --- Basic Setup & Domain ---
hostname R-EDGE
no ip domain-lookup
ip domain-name netadmin.local

! --- Security & SSH ---
username admin privilege 15 secret AdminPass123!
crypto key generate rsa general-keys modulus 2048
ip ssh version 2

! --- Interfaces ---
interface Loopback0
 description OSPF Router ID
 ip address 1.1.1.1 255.255.255.255
 no shutdown

interface GigabitEthernet0/0
 description Link to R-CORE2 G0/0
 ip address 10.0.0.5 255.255.255.252
 ip nat inside
 no shutdown

interface GigabitEthernet0/1
 description Link to R-CORE1 G0/1
 ip address 10.0.0.1 255.255.255.252
 ip nat inside
 no shutdown

interface GigabitEthernet0/2
 description WAN Link to R-ISP G0/0
 ip address 203.0.113.2 255.255.255.252
 ip nat outside
 no shutdown

! --- OSPF Configuration ---
router ospf 1
 router-id 1.1.1.1
 network 1.1.1.1 0.0.0.0 area 0
 network 10.0.0.0 0.0.0.3 area 0
 network 10.0.0.4 0.0.0.3 area 0
 default-information originate

! --- Static Route to ISP ---
ip route 0.0.0.0 0.0.0.0 203.0.113.1

! --- Access Control List for NAT (Includes VLANs 10, 20, 93) ---
ip access-list standard NAT-ACL
 permit 192.168.10.0 0.0.0.255
 permit 172.16.20.0 0.0.0.255
 permit 172.20.0.0 0.0.0.15

! --- Dynamic NAT Overload (PAT) ---
ip nat inside source list NAT-ACL interface GigabitEthernet0/2 overload

! --- Line Security ---
line con 0
 logging synchronous
 login local
line vty 0 4
 transport input ssh
 login local

end
write memory

```

---

## Part E: Full Cisco IOS Configuration — R-CORE1

```cisconetworking
enable
configure terminal

! --- Basic Setup & SSH ---
hostname R-CORE1
no ip domain-lookup
ip domain-name netadmin.local

username admin privilege 15 secret AdminPass123!
crypto key generate rsa general-keys modulus 2048
ip ssh version 2

! --- Interfaces ---
interface Loopback0
 description OSPF Router ID
 ip address 2.2.2.2 255.255.255.255
 no shutdown

interface GigabitEthernet0/0
 description Link to R-CORE2 G0/1
 ip address 10.0.0.9 255.255.255.252
 no shutdown

interface GigabitEthernet0/1
 description Link to R-EDGE G0/1
 ip address 10.0.0.2 255.255.255.252
 no shutdown

! --- OSPF Configuration ---
router ospf 1
 router-id 2.2.2.2
 network 2.2.2.2 0.0.0.0 area 0
 network 10.0.0.0 0.0.0.3 area 0
 network 10.0.0.8 0.0.0.3 area 0

! --- Line Security ---
line con 0
 logging synchronous
 login local
line vty 0 4
 transport input ssh
 login local

end
write memory

```

---

## Part F: Full Cisco IOS Configuration — R-CORE2

```cisconetworking
enable
configure terminal

! --- Basic Setup & SSH ---
hostname R-CORE2
no ip domain-lookup
ip domain-name netadmin.local

username admin privilege 15 secret AdminPass123!
crypto key generate rsa general-keys modulus 2048
ip ssh version 2

! --- Loopback & Transit Interfaces ---
interface Loopback0
 description OSPF Router ID
 ip address 3.3.3.3 255.255.255.255
 no shutdown

interface GigabitEthernet0/0
 description Link to R-EDGE G0/0
 ip address 10.0.0.6 255.255.255.252
 no shutdown

interface GigabitEthernet0/1
 description Link to R-CORE1 G0/0
 ip address 10.0.0.10 255.255.255.252
 no shutdown

! --- Router-on-a-Stick (ROAS) Trunk Base ---
interface GigabitEthernet0/2
 description Trunk to SW-DIST G0/1
 no shutdown

interface GigabitEthernet0/2.10
 description Gateway for VLAN 10 (SALES)
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

interface GigabitEthernet0/2.20
 description Gateway for VLAN 20 (IT-ADMIN)
 encapsulation dot1Q 20
 ip address 172.16.20.1 255.255.255.0

interface GigabitEthernet0/2.93
 description Gateway for VLAN 93 (MANAGEMENT)
 encapsulation dot1Q 93
 ip address 172.20.0.1 255.255.255.240

! --- OSPF Configuration ---
router ospf 1
 router-id 3.3.3.3
 network 3.3.3.3 0.0.0.0 area 0
 network 10.0.0.4 0.0.0.3 area 0
 network 10.0.0.8 0.0.0.3 area 0
 network 192.168.10.0 0.0.0.255 area 0
 network 172.16.20.0 0.0.0.255 area 0
 network 172.20.0.0 0.0.0.15 area 0
 passive-interface GigabitEthernet0/2.10
 passive-interface GigabitEthernet0/2.20
 passive-interface GigabitEthernet0/2.93

! --- Line Security ---
line con 0
 logging synchronous
 login local
line vty 0 4
 transport input ssh
 login local

end
write memory

```

---

## Part G: Full Cisco IOS Configuration — SW-DIST

```cisconetworking
enable
configure terminal

! --- Basic Setup & Security ---
hostname SW-DIST
no ip domain-lookup
ip domain-name netadmin.local

username admin privilege 15 secret AdminPass123!
crypto key generate rsa general-keys modulus 2048
ip ssh version 2

! --- VLAN Creation ---
vlan 10
 name SALES
vlan 20
 name IT-ADMIN
vlan 93
 name MANAGEMENT
exit

! --- Rapid Spanning Tree Protocol (Root Bridge) ---
spanning-tree mode rapid-pvst
spanning-tree vlan 10,20,93 root primary

! --- Trunk Interfaces ---
interface GigabitEthernet0/1
 description Router Trunk to R-CORE2 G0/2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20,93
 no shutdown

interface FastEthernet0/1
 description Trunk to SW-ACCESS1 Fa0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20,93
 no shutdown

interface FastEthernet0/2
 description Trunk to SW-ACCESS2 Fa0/2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20,93
 no shutdown

! --- Switch Management SVI ---
interface vlan 93
 description Management SVI
 ip address 172.20.0.2 255.255.255.240
 no shutdown

ip default-gateway 172.20.0.1

! --- Port Security Hardening (Unused Ports Shutdown) ---
interface range FastEthernet0/3-24, GigabitEthernet0/2
 shutdown

! --- Line Security ---
line con 0
 logging synchronous
 login local
line vty 0 4
 transport input ssh
 login local

end
write memory

```

---

## Part H: Full Cisco IOS Configuration — SW-ACCESS1

```cisconetworking
enable
configure terminal

! --- Basic Setup & SSH ---
hostname SW-ACCESS1
no ip domain-lookup
ip domain-name netadmin.local

username admin privilege 15 secret AdminPass123!
crypto key generate rsa general-keys modulus 2048
ip ssh version 2

! --- VLAN Database ---
vlan 10
 name SALES
vlan 20
 name IT-ADMIN
vlan 93
 name MANAGEMENT
exit

! --- STP Mode & Secondary Root ---
spanning-tree mode rapid-pvst
spanning-tree vlan 10,20,93 root secondary

! --- Trunk Ports ---
interface FastEthernet0/1
 description Trunk to SW-DIST Fa0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,93
 no shutdown

interface FastEthernet0/3
 description Trunk to SW-ACCESS2 Fa0/3
 switchport mode trunk
 switchport trunk allowed vlan 10,20,93
 no shutdown

! --- Access Ports (End Devices) ---
interface range FastEthernet0/21 - 22
 description Access Ports - SALES (VLAN 10)
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 spanning-tree bpduguard enable
 no shutdown

interface range FastEthernet0/23 - 24
 description Access Ports - IT-ADMIN (VLAN 20)
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 spanning-tree bpduguard enable
 no shutdown

! --- Switch Management SVI ---
interface vlan 93
 description Management SVI
 ip address 172.20.0.3 255.255.255.240
 no shutdown

ip default-gateway 172.20.0.1

! --- Port Security Hardening (Unused Ports Shutdown) ---
interface range FastEthernet0/2, FastEthernet0/4-20, GigabitEthernet0/1-2
 shutdown

! --- Line Security ---
line con 0
 logging synchronous
 login local
line vty 0 4
 transport input ssh
 login local

end
write memory

```

---

## Part I: Full Cisco IOS Configuration — SW-ACCESS2

```cisconetworking
enable
configure terminal

! --- Basic Setup & SSH ---
hostname SW-ACCESS2
no ip domain-lookup
ip domain-name netadmin.local

username admin privilege 15 secret AdminPass123!
crypto key generate rsa general-keys modulus 2048
ip ssh version 2

! --- VLAN Database ---
vlan 10
 name SALES
vlan 20
 name IT-ADMIN
vlan 93
 name MANAGEMENT
exit

! --- STP Mode ---
spanning-tree mode rapid-pvst

! --- Trunk Ports ---
interface FastEthernet0/2
 description Trunk to SW-DIST Fa0/2
 switchport mode trunk
 switchport trunk allowed vlan 10,20,93
 no shutdown

interface FastEthernet0/3
 description Trunk to SW-ACCESS1 Fa0/3
 switchport mode trunk
 switchport trunk allowed vlan 10,20,93
 no shutdown

! --- Access Ports (End Devices) ---
interface range FastEthernet0/11 - 12
 description Access Ports - MANAGEMENT (VLAN 93)
 switchport mode access
 switchport access vlan 93
 spanning-tree portfast
 spanning-tree bpduguard enable
 no shutdown

! --- Switch Management SVI ---
interface vlan 93
 description Management SVI
 ip address 172.20.0.4 255.255.255.240
 no shutdown

ip default-gateway 172.20.0.1

! --- Port Security Hardening (Unused Ports Shutdown) ---
interface range FastEthernet0/1, FastEthernet0/4-10, FastEthernet0/13-24, GigabitEthernet0/1-2
 shutdown

! --- Line Security ---
line con 0
 logging synchronous
 login local
line vty 0 4
 transport input ssh
 login local

end
write memory

```

---

## Part J: Static IP Configuration Table for End-Devices

| Host Name | Connected Switch / Port | VLAN ID | IP Address | Subnet Mask | Default Gateway | Primary DNS |
| --- | --- | --- | --- | --- | --- | --- |
| **PC0** | SW-ACCESS1 / Fa0/21 | 10 | `192.168.10.10` | `255.255.255.0` | `192.168.10.1` | `8.8.8.8` |
| **PC1** | SW-ACCESS1 / Fa0/22 | 10 | `192.168.10.11` | `255.255.255.0` | `192.168.10.1` | `8.8.8.8` |
| **PC2** | SW-ACCESS1 / Fa0/23 | 20 | `172.16.20.10` | `255.255.255.0` | `172.16.20.1` | `8.8.8.8` |
| **PC3** | SW-ACCESS1 / Fa0/24 | 20 | `172.16.20.11` | `255.255.255.0` | `172.16.20.1` | `8.8.8.8` |
| **PC4** | SW-ACCESS2 / Fa0/12 | 93 | `172.20.0.10` | `255.255.255.240` | `172.20.0.1` | `8.8.8.8` |
| **PC5** | SW-ACCESS2 / Fa0/11 | 93 | `172.20.0.11` | `255.255.255.240` | `172.20.0.1` | `8.8.8.8` |

---

## Part K: Verification Commands

### Layer 2 Verification (Switches)

```cisconetworking
! Check VLAN database mapping
show vlan brief

! Validate trunk state and allowed VLAN list
show interfaces trunk

! Validate Rapid-PVST+ states and verify Root Bridge assignment
show spanning-tree
show spanning-tree vlan 10
show spanning-tree vlan 20
show spanning-tree vlan 93

! Dynamic MAC Table verification
show mac address-table

! Verify SVI Status
show ip interface brief

```

### Layer 3 & Routing Verification (Routers)

```cisconetworking
! Interface IP operational check
show ip interface brief

! Check complete routing table
show ip route

! Check OSPF learned routes specifically
show ip route ospf

! Verify OSPF Neighbor Adjacency states (FULL/DR or FULL/BDR or FULL/2WAY)
show ip ospf neighbor

! Verify interface status, costs, and passive state
show ip ospf interface

! Verify dynamic routing process parameters
show ip protocols

```

### NAT & SSH Verification (R-EDGE / Switches)

```cisconetworking
! Verify active source NAT dynamic translations
show ip nat translations

! Check packet counters and active pool statistics
show ip nat statistics

! Verify SSH operational mode on all devices
show ip ssh
show users

```

---

## Part L: Revalida Troubleshooting Procedure

### 1. VLAN & Trunking Issues

1. Execute `show vlan brief` on switches to verify that target access ports are explicitly assigned to their intended VLANs (VLAN 10, 20, or 93).
2. Execute `show interfaces trunk` on all inter-switch and router-facing links:
* Confirm mode is `on` or `desirable/auto` resulting in active trunking.
* Verify native VLAN alignment across links.
* Ensure `10, 20, 93` are within the **VLANs allowed on trunk** list.


3. Validate subinterface encapsulation on `R-CORE2` using `show ip interface brief`. Confirm subinterface dot1q IDs strictly match VLAN IDs (`.10` = 10, `.20` = 20, `.93` = 93).

### 2. Spanning Tree Protocol (STP) Loops / Discarding Issues

1. Run `show spanning-tree vlan <id>` on `SW-DIST`. Verify it states `"This bridge is the root"`.
2. Inspect interface roles across access switches (`SW-ACCESS1`, `SW-ACCESS2`). Confirm exactly one port in the redundant switch triangle enters `BLK`/`DISC` (Alternate/Blocking state) to verify loop prevention.
3. If an access port drops unexpectedly when a host connects, run `show interface <type/num>` to verify if **BPDU Guard** placed the interface into an `err-disable` state due to receiving unexpected BPDUs. Recover using `shutdown` then `no shutdown`.

### 3. OSPF Neighbor & Routing Failures

1. Run `show ip ospf neighbor` on `R-EDGE`, `R-CORE1`, and `R-CORE2`. Adjacency status must settle at `FULL`.
2. If stuck in `INIT` or `2-WAY`:
* Verify subnets match `/30` on point-to-point links.
* Check for MTU mismatches (`ip ospf mtu-ignore` if needed in non-standard lab environments).
* Ensure OSPF `hello` and `dead` timers match on adjacent interfaces.


3. If routes are missing on `R-EDGE` or `R-CORE1`:
* Confirm network statements cover inter-router link subnets correctly in `router ospf 1`.
* Verify `default-information originate` is present on `R-EDGE` and that `R-EDGE` maintains a valid static default route pointing to `R-ISP` (`203.0.113.1`).



### 4. NAT / External Internet Access Failures

1. Execute `show ip nat statistics` on `R-EDGE`.
2. Verify interface bindings:
* `GigabitEthernet0/0` and `GigabitEthernet0/1` must be `NAT inside`.
* `GigabitEthernet0/2` must be `NAT outside`.


3. Verify access control list `NAT-ACL`:
* Run `show access-lists NAT-ACL`.
* Confirm the permit rules cover `192.168.10.0/24`, `172.16.20.0/24`, and `172.20.0.0/28`.


4. Ping `8.8.8.8` from an end device (`PC0`) while executing `show ip nat translations` on `R-EDGE` to confirm active translation entries.