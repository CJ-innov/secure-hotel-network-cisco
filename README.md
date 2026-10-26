# Secure 3-Floor Hotel Network, Cisco Packet Tracer

A segmented, fault-tolerant enterprise network for a three-floor hotel, built and secured in Cisco Packet Tracer. The IT, Admin, and Finance departments are strictly isolated using VLANs, extended ACLs, port security, and SSH-only management.

*Academic project: Cybersecurity Department, Seven Advanced Academy, Douala, Cameroon (2025–2026).*

## Highlights
* **Hybrid Topology:** Full-mesh router core ($3 \times$ Cisco 2911) with a star-style access layer (Catalyst 2960 per floor).
* **High Availability:** OSPF routing configured with automatic failover if a core link drops.
* **Inter-VLAN Routing:** Router-on-a-stick architecture utilizing 802.1Q subinterfaces.
* **Access Control:** Extended ACLs enforcing strict least-privilege boundaries between departments.
* **Endpoint Hardening:** Port security with sticky MAC addresses and automated `err-disabled` states on violations.
* **Management Security:** SSH v2 enforced exclusively for device administration; Telnet completely disabled.

---

## Network Addressing & VLAN Allocation

| Floor | Department | VLAN ID | Subnet / Mask |
| :--- | :--- | :--- | :--- |
| **1st Floor** | Reception | 10 | `192.168.10.0/24` |
| **1st Floor** | Store | 20 | `192.168.11.0/24` |
| **1st Floor** | Logistics | 30 | `192.168.12.0/24` |
| **2nd Floor** | HR | 80 | `192.168.20.0/24` |
| **2nd Floor** | Finance | 70 | `192.168.21.0/24` |
| **2nd Floor** | Sales & Marketing | 60 | `192.168.22.0/24` |
| **3rd Floor** | Admin | 50 | `192.168.30.0/24` |
| **3rd Floor** | IT | 40 | `192.168.31.0/24` |

* **Core Links:** `10.10.10.0/30` (F1–F3), `10.10.10.4/30` (F3–F2), `10.10.10.8/30` (F1–F2).

---

## Security Policy Matrix

| Source ↓ / Destination → | IT | Admin | Finance | Other Departments |
| :--- | :--- | :--- | :--- | :--- |
| **IT** | Allow | Allow | Allow | Allow |
| **Admin / Finance** | Replies Only | Own Subnet | Own Subnet | Allow |
| **Other Departments** | Deny | Deny | Deny | Allow |

* **Architectural Note:** IT maintains deliberate one-way administrative reach for support and patching. Admin and Finance cannot initiate inbound connections into IT, strictly limiting lateral movement in the event of a compromise.
* Full ACL, SSH, and port security commands are documented in [security-configs.md](security-configs.md).

---

## Test Results & Validation

| Test Description | Result |
| :--- | :---: |
| Access ports assigned to correct VLANs (`show vlan brief`) | **Pass** |
| Telnet connections refused; SSH v2 succeeds | **Pass** |
| Unauthorised PC plugged into a secured port triggers `err-disabled` | **Pass** |
| Reception, HR, and Sales blocked from IT, Admin, and Finance | **Pass** |
| IT successfully reaches Admin and Finance | **Pass** |
| ACL deny counters increment during negative testing (`show access-lists`) | **Pass** |
| OSPF dynamically reroutes traffic when a core link is disconnected | **Pass** |

---

## Lessons Learned
* **ACL Binding:** A configured ACL isn't automatically an active ACL. An initial oversight bound an ACL to an unused subinterface, rendering it ineffective until `show ip interface` and hit counters exposed the mismatch.
* **Stateless Filtering:** ACLs are stateless. Requests were permitted outward, but return traffic (like TCP established handshakes and ICMP echo-replies) was dropped on the return path until explicit return-path allowances were integrated.
* **Addressing Discipline:** Maintaining strict alignment between VLAN IDs, subinterface numbers, and subnet octets prevents encapsulation mismatches during dot1Q configuration.

---

## Limitations & Next Steps
* **Stateful Inspection:** `tcp established` can theoretically be spoofed; implementing a Zone-Based Firewall or reflexive ACLs would provide true stateful packet inspection.
* **Future Enhancements:** Least-privilege hardening for IT (restricting administrative access to specific destination ports), 802.1X / RADIUS authentication, and EtherChannel trunk aggregation across the redundant core.

---

## 📁 Repository Structure
```text
├── README.md
└── security-configs.md

Allow approximately 30 seconds for OSPF convergence.

Validate routing and security enforcement using show ip ospf neighbor and show access-lists.

 Author
Chia Joshua Vernyuy Nyang, Junior SOC Analyst.

joshuachia67@gmail.com
