# Network Security Configurations

This document contains reference configurations for SSH management hardening, port security, and extended Access Control Lists (ACLs) implemented across the hotel network devices.

## 1. SSH v2 Management Hardening (Core & Access Switches)
```c
ip domain-name hotel.local
crypto key generate rsa modulus 2048
ip ssh version 2

username admin privilege 15 secret SecureAdminPassword123!

line vty 0 4
 transport input ssh
 login local
2. Port Security Configuration (Access Layer Switches)
C
interface range FastEthernet 0/1 - 24
 switchport mode access
 switchport port-security
 switchport port-security maximum 1
 switchport port-security violation restrict
 switchport port-security mac-address sticky
3. Extended ACL Implementation (Inter-VLAN Isolation)
C
! Example Extended ACL restricting unauthorized access to IT & Finance
ip access-list extended BLOCK_UNAUTHORIZED_ACCESS
 deny ip 192.168.10.0 0.0.0.255 192.168.31.0 0.0.0.255
 deny ip 192.168.20.0 0.0.0.255 192.168.31.0 0.0.0.255
 permit ip any any

interface GigabitEthernet 0/0.40
 ip access-group BLOCK_UNAUTHORIZED_ACCESS in
