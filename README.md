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



