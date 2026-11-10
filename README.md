# Cisco-ASA-Full-Firewall-Configuration-in-Packet-Tracer-NAT-ACL-VLAN-ICMP Test

## Cisco ASA Firewall Rules in Packet Tracer

* Cisco ASA Basic Setuo
* Access Rules Configuration
* NAT (Dynamic & Static)
* Verifying with ping and packet tracer

ASA must NAT inside IPs to the ASA`s outside IP.
* Route must have a route back to the ASA`s external subnet.
* ASA must allow inside-to-outside traffic.
* Outside ping attempts must be denied explicitly.

## Lab Topology

PC0 (inside): 192.168.1.10/24
ASA Insider IP: 192.168.1.1/24
ASA Outside IP: 10.0.0.1/24
Router Outsider IP: 10.0.0.2/24
PC1(Outsider): 172.16.0.10/24

## 1. FIREWALL CONFIGURATION

### Cisco ASA Configuration (ASA 8.4)

This configuration sets up a Cisco ASA 5505 with basic routing, Dynamic PAT (NAT) for outbound internet access, DHCP services for internal clients, and Access Control Lists (ACLs).

### 1. Basic Device & Logical Interfaces (VLANs)
```cisco
enable
configure terminal

hostname ciscoasa
domain-name wr

! Configure Inside Interface
interface Vlan1
 nameif inside
 security-level 100
 ip address 192.168.1.1 255.255.255.0
 no shutdown
exit

! Configure Outside Interface
interface Vlan2
 nameif outside
 security-level 0
 ip address 10.0.0.1 255.255.255.0
 no shutdown
exit
```

### 2. Assign Physical Interfaces to VLANs
*(Note: Switchports default to VLAN 1 on the ASA 5505, so only VLAN 2 needs explicit assignment).*
```cisco
! Assign Ethernet0/0 to Outside (VLAN 2)
interface Ethernet0/0
 switchport access vlan 2
 no shutdown
exit

! Ensure Ethernet0/1 (Inside) is active
interface Ethernet0/1
 no shutdown
exit
```

### 3. Configure Dynamic PAT (NAT)
Translates internal `192.168.1.0/24` addresses to the `outside` interface IP for internet access.
```cisco
object network INSIDE-NET
 subnet 192.168.1.0 255.255.255.0
 nat (inside,outside) dynamic interface
exit
```

### 4. Configure Default Route
```cisco
! Set default gateway for outbound traffic
route outside 0.0.0.0 0.0.0.0 10.0.0.2 1
```

### 5. Configure DHCP Server (Inside)
Automatically assigns IP addresses (`192.168.1.5` – `192.168.1.36`) to devices connected to the inside network.
```cisco
dhcpd auto_config outside
dhcpd address 192.168.1.5-192.168.1.36 inside
dhcpd enable inside
```

### 6. Configure Access Control Lists (ACLs)
> ⚠️ **Security Note:** Currently, `OUTSIDE_ACL` permits `ip any any` inbound from the outside interface. In a production environment, restrict this to only specific required ports (e.g., ICMP, HTTP/S, VPN).

```cisco
! Define ACLs
access-list INSIDE_ACL extended permit ip any any
access-list OUTSIDE_ACL extended permit ip any any

! Apply ACLs to Interfaces
access-group INSIDE_ACL in interface inside
access-group OUTSIDE_ACL in interface outside
```

### 7. Save Configuration
```cisco
end
copy running-config startup-config
```

## 2. Router CONFIGURATION



