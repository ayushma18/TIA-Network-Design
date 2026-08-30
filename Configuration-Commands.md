# Configuration Commands — Tribhuvan International Airport (TIA) Network

**Ayushma Pudasaini (079BCT030) — Computer Networks Mini-Project**

This file lists the Cisco IOS commands used to configure every device in the network, grouped by device and function: basic setup and remote access, interface addressing, OSPF, edge routing, VLANs and inter-VLAN routing, DHCP, and switch configuration.

**Passwords:** console = `cisco`, privileged (enable) = `class`, telnet (vty) = `network`.

---

## A. Basic Setup and Remote Access (every router)

Applied on every router (Ayushma1–Ayushma10); only the hostname changes.

```
Router> enable
Router# configure terminal
Router(config)# hostname Ayushma1
Ayushma1(config)# enable secret class
Ayushma1(config)# service password-encryption
Ayushma1(config)# banner motd #Ayushma Pudasaini 079BCT030 - Authorized access only#
Ayushma1(config)# line console 0
Ayushma1(config-line)#  password cisco
Ayushma1(config-line)#  login
Ayushma1(config-line)#  exit
Ayushma1(config)# line vty 0 4
Ayushma1(config-line)#  password network
Ayushma1(config-line)#  login
Ayushma1(config-line)#  transport input telnet
Ayushma1(config-line)#  exit
Ayushma1(config)# end
Ayushma1# write memory
```

---

## B. Interface Addressing and OSPF (per router)

### Ayushma1 — Edge (WAN uplink, default route)

```
interface Serial0/0/0
 ip address 203.0.113.2 255.255.255.252
 no shutdown
interface GigabitEthernet0/0
 ip address 172.20.15.1 255.255.255.252
 no shutdown
interface GigabitEthernet0/1
 ip address 172.20.15.5 255.255.255.252
 no shutdown
router ospf 1
 router-id 1.1.1.1
 network 172.20.15.0 0.0.0.3 area 0
 network 172.20.15.4 0.0.0.3 area 0
 default-information originate
ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

### Ayushma2 — Core (Server Farm A)

```
interface GigabitEthernet0/0
 ip address 172.20.15.2 255.255.255.252
 no shutdown
interface GigabitEthernet0/1
 ip address 172.20.14.1 255.255.255.240
 no shutdown
interface GigabitEthernet0/2
 ip address 172.20.15.13 255.255.255.252
 no shutdown
interface Serial0/0/0
 ip address 172.20.15.9 255.255.255.252
 clock rate 2000000
 no shutdown
interface Serial0/0/1
 ip address 172.20.15.17 255.255.255.252
 clock rate 2000000
 no shutdown
router ospf 1
 router-id 2.2.2.2
 network 172.20.15.0  0.0.0.3  area 0
 network 172.20.15.8  0.0.0.3  area 0
 network 172.20.14.0  0.0.0.15 area 0
 network 172.20.15.12 0.0.0.3  area 0
 network 172.20.15.16 0.0.0.3  area 0
```

### Ayushma3 — Core (transit)

```
interface GigabitEthernet0/0
 ip address 172.20.15.6 255.255.255.252
 no shutdown
interface GigabitEthernet0/1
 ip address 172.20.15.21 255.255.255.252
 no shutdown
interface GigabitEthernet0/2
 ip address 172.20.15.25 255.255.255.252
 no shutdown
interface Serial0/0/0
 ip address 172.20.15.10 255.255.255.252
 no shutdown
router ospf 1
 router-id 3.3.3.3
 network 172.20.15.4  0.0.0.3 area 0
 network 172.20.15.8  0.0.0.3 area 0
 network 172.20.15.20 0.0.0.3 area 0
 network 172.20.15.24 0.0.0.3 area 0
```

### Ayushma4 — Backbone transit

```
interface GigabitEthernet0/0
 ip address 172.20.15.26 255.255.255.252
 no shutdown
interface GigabitEthernet0/1
 ip address 172.20.15.29 255.255.255.252
 no shutdown
interface GigabitEthernet0/2
 ip address 172.20.15.33 255.255.255.252
 no shutdown
router ospf 1
 router-id 4.4.4.4
 network 172.20.15.24 0.0.0.3 area 0
 network 172.20.15.28 0.0.0.3 area 0
 network 172.20.15.32 0.0.0.3 area 0
```

### Ayushma5 — Terminal ABR (Server Farm B)

```
interface GigabitEthernet0/0
 ip address 172.20.15.14 255.255.255.252
 no shutdown
interface GigabitEthernet0/1
 ip address 172.20.15.22 255.255.255.252
 no shutdown
interface GigabitEthernet0/2
 ip address 172.20.14.17 255.255.255.240
 no shutdown
interface Serial0/0/0
 ip address 172.20.15.37 255.255.255.252
 clock rate 2000000
 no shutdown
router ospf 1
 router-id 5.5.5.5
 network 172.20.15.12 0.0.0.3  area 0
 network 172.20.15.20 0.0.0.3  area 0
 network 172.20.14.16 0.0.0.15 area 1
 network 172.20.15.36 0.0.0.3  area 1
```

### Ayushma6 — Inter-VLAN routing (router-on-a-stick)

```
interface Serial0/0/0
 ip address 172.20.15.38 255.255.255.252
 no shutdown
interface GigabitEthernet0/0
 no shutdown
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 172.20.6.1 255.255.255.0
interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 172.20.0.1 255.255.252.0
interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 172.20.7.129 255.255.255.128
router ospf 1
 router-id 6.6.6.6
 network 172.20.15.36 0.0.0.3   area 1
 network 172.20.6.0   0.0.0.255 area 1
 network 172.20.0.0   0.0.3.255 area 1
 network 172.20.7.128 0.0.0.127 area 1
```

### Ayushma7 — Security ABR (CCTV)

```
interface Serial0/0/0
 ip address 172.20.15.18 255.255.255.252
 no shutdown
interface GigabitEthernet0/0
 ip address 172.20.15.30 255.255.255.252
 no shutdown
interface GigabitEthernet0/1
 ip address 172.20.4.1 255.255.254.0
 no shutdown
interface GigabitEthernet0/2
 ip address 172.20.15.41 255.255.255.252
 no shutdown
router ospf 1
 router-id 7.7.7.7
 network 172.20.15.16 0.0.0.3   area 0
 network 172.20.15.28 0.0.0.3   area 0
 network 172.20.4.0   0.0.1.255 area 2
 network 172.20.15.40 0.0.0.3   area 2
```

### Ayushma8 — Immigration and Baggage

```
interface GigabitEthernet0/0
 ip address 172.20.15.42 255.255.255.252
 no shutdown
interface GigabitEthernet0/1
 ip address 172.20.8.1 255.255.255.192
 no shutdown
interface GigabitEthernet0/2
 ip address 172.20.7.1 255.255.255.128
 no shutdown
router ospf 1
 router-id 8.8.8.8
 network 172.20.15.40 0.0.0.3   area 2
 network 172.20.8.0   0.0.0.63  area 2
 network 172.20.7.0   0.0.0.127 area 2
```

### Ayushma9 — Cargo ABR (Cargo and Admin)

```
interface GigabitEthernet0/0
 ip address 172.20.15.34 255.255.255.252
 no shutdown
interface GigabitEthernet0/1
 ip address 172.20.8.65 255.255.255.192
 no shutdown
interface GigabitEthernet0/2
 ip address 172.20.8.129 255.255.255.224
 no shutdown
router ospf 1
 router-id 9.9.9.9
 network 172.20.15.32 0.0.0.3  area 0
 network 172.20.8.64  0.0.0.63 area 3
 network 172.20.8.128 0.0.0.31 area 3
```

### Ayushma10 — ISP (single static return route, no dynamic routing)

```
interface Serial0/0/0
 ip address 203.0.113.1 255.255.255.252
 clock rate 2000000
 no shutdown
interface GigabitEthernet0/0
 ip address 203.0.113.9 255.255.255.248
 no shutdown
ip route 172.20.0.0 255.255.240.0 203.0.113.2
```

---

## C. VLANs, Trunks and Inter-VLAN Routing

Create the VLANs on each terminal switch (SW-T1, SW-T2, SW-T3):

```
Switch> enable
Switch# configure terminal
Switch(config)# vlan 10
Switch(config-vlan)#  name CHECKIN
Switch(config)# vlan 20
Switch(config-vlan)#  name PUBWIFI
Switch(config)# vlan 30
Switch(config-vlan)#  name AIRLINE
```

Assign access ports (example: a Check-in PC) and set the trunks:

```
interface FastEthernet0/1
 switchport mode access
 switchport access vlan 10
interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
interface GigabitEthernet0/2
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
```

Inter-VLAN routing is done on **Ayushma6** using the dot1Q subinterfaces shown in Section B.

---

## D. DHCP (one pool per LAN, on the gateway router)

> Note: this version of Packet Tracer accepts only a single address on the `dns-server` line, so the primary DNS `172.20.14.2` is used.

### Ayushma6 — the three terminal VLANs

```
ip dhcp excluded-address 172.20.6.1 172.20.6.30
ip dhcp excluded-address 172.20.0.1 172.20.0.30
ip dhcp excluded-address 172.20.7.129 172.20.7.150
ip dhcp pool VLAN10-CHECKIN
 network 172.20.6.0 255.255.255.0
 default-router 172.20.6.1
 dns-server 172.20.14.2
ip dhcp pool VLAN20-WIFI
 network 172.20.0.0 255.255.252.0
 default-router 172.20.0.1
 dns-server 172.20.14.2
ip dhcp pool VLAN30-AIRLINE
 network 172.20.7.128 255.255.255.128
 default-router 172.20.7.129
 dns-server 172.20.14.2
```

### Ayushma7 (CCTV), Ayushma8 (Immigration, Baggage), Ayushma9 (Cargo, Admin)

```
! Ayushma7
ip dhcp excluded-address 172.20.4.1 172.20.4.30
ip dhcp pool CCTV
 network 172.20.4.0 255.255.254.0
 default-router 172.20.4.1
 dns-server 172.20.14.2

! Ayushma8
ip dhcp excluded-address 172.20.8.1 172.20.8.30
ip dhcp excluded-address 172.20.7.1 172.20.7.30
ip dhcp pool IMMIGRATION
 network 172.20.8.0 255.255.255.192
 default-router 172.20.8.1
 dns-server 172.20.14.2
ip dhcp pool BAGGAGE
 network 172.20.7.0 255.255.255.128
 default-router 172.20.7.1
 dns-server 172.20.14.2

! Ayushma9
ip dhcp excluded-address 172.20.8.65 172.20.8.80
ip dhcp excluded-address 172.20.8.129 172.20.8.140
ip dhcp pool CARGO
 network 172.20.8.64 255.255.255.192
 default-router 172.20.8.65
 dns-server 172.20.14.2
ip dhcp pool CAAN-ADMIN
 network 172.20.8.128 255.255.255.224
 default-router 172.20.8.129
 dns-server 172.20.14.2
```

---

## E. Switch Configuration (passwords, telnet and management IP)

Every switch was given the same passwords, telnet access, and a management IP so it can be reached remotely.
Example for **SW-CCTV** (management address `172.20.4.5`):

```
Switch> enable
Switch# configure terminal
Switch(config)# enable secret class
Switch(config)# service password-encryption
Switch(config)# line console 0
Switch(config-line)#  password cisco
Switch(config-line)#  login
Switch(config)# line vty 0 4
Switch(config-line)#  password network
Switch(config-line)#  login
Switch(config-line)#  transport input telnet
Switch(config)# interface vlan 1
Switch(config-if)#  ip address 172.20.4.5 255.255.254.0
Switch(config-if)#  no shutdown
Switch(config)# ip default-gateway 172.20.4.1
Switch(config)# end
Switch# write memory
```

**Management IP address of each switch:**

| Switch | Management IP | VLAN | Gateway |
|---|---|---|---|
| SW-ISP | 203.0.113.12 /29 | 1 | 203.0.113.9 |
| SW-SRV1 | 172.20.14.5 /28 | 1 | 172.20.14.1 |
| SW-SRV2 | 172.20.14.21 /28 | 1 | 172.20.14.17 |
| SW-CCTV | 172.20.4.5 /23 | 1 | 172.20.4.1 |
| SW-IMMIG | 172.20.8.5 /26 | 1 | 172.20.8.1 |
| SW-BAG | 172.20.7.5 /25 | 1 | 172.20.7.1 |
| SW-CARGO | 172.20.8.69 /26 | 1 | 172.20.8.65 |
| SW-ADMIN | 172.20.8.133 /27 | 1 | 172.20.8.129 |
| SW-T1 | 172.20.0.5 /22 | 20 | 172.20.0.1 |
| SW-T2 | 172.20.0.6 /22 | 20 | 172.20.0.1 |
| SW-T3 | 172.20.0.7 /22 | 20 | 172.20.0.1 |

---

## F. Verification Commands

```
show ip interface brief          ! interface up/up and addresses
show ip ospf neighbor            ! OSPF adjacencies (FULL state)
show ip route                    ! routing table (O, O IA, O*E2)
show ip protocols                ! OSPF process, router-id, networks
show vlan brief                  ! VLANs and their access ports
show interfaces trunk            ! trunk ports and allowed VLANs
show ip dhcp binding             ! DHCP addresses handed out
show running-config              ! full configuration of the device
```
