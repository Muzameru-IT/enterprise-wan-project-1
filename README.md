# Multi-Site Enterprise WAN — Configuration & Troubleshooting Lab

## Project Overview

This project simulates a multi-site enterprise WAN connecting a corporate headquarters with Chicago and Atlanta branch offices using Cisco Packet Tracer.

The project focuses on both network implementation and real-world troubleshooting.

The network was first built and validated end-to-end. Multiple faults were then deliberately introduced, diagnosed, corrected, and verified.

---

## Network Architecture

### Sites

- Corporate Headquarters (HQ)
- Chicago Branch
- Atlanta Branch

### WAN Design

Hub-and-spoke topology:

Chicago Branch
      |
      |
     HQ
      |
      |
Atlanta Branch

HQ acts as the central WAN transit point for branch-to-branch communication.

---

## Technologies Implemented

- IPv4 subnetting
- /30 point-to-point WAN addressing
- VLAN segmentation
- IEEE 802.1Q trunking
- Native VLAN configuration
- Router-on-a-stick
- Inter-VLAN routing
- DHCP
- DHCP excluded address ranges
- Voice VLAN
- Static routing
- Default routing
- Serial WAN links
- Cisco HDLC/PPP troubleshooting
- Branch-to-branch routing
- Network management VLAN
- Ping and traceroute validation

---

## VLAN Design

### Headquarters

| VLAN | Purpose | Network |
|------|---------|---------|
| 10 | Management | 192.168.10.0/24 |
| 20 | Employees | 192.168.20.0/24 |
| 30 | Servers | 192.168.30.0/24 |
| 40 | Voice | 192.168.40.0/24 |
| 99 | Native | 192.168.99.0/24 |

### Chicago

| VLAN | Purpose | Network |
|------|---------|---------|
| 20 | Employees | 192.168.120.0/24 |
| 30 | Servers | 192.168.130.0/24 |
| 99 | Native | 192.168.199.0/24 |

### Atlanta

| VLAN | Purpose | Network |
|------|---------|---------|
| 20 | Employees | 192.168.220.0/24 |
| 30 | Printers | 192.168.230.0/24 |
| 99 | Native | 192.168.250.0/24 |

---

## WAN Addressing

Point-to-point /30 networks were used between routers to conserve IPv4 addresses.

Example:

HQ ↔ Chicago

R-HQ: 10.0.0.1/30
R-CHI: 10.0.0.2/30

HQ ↔ Atlanta

R-HQ: 10.0.0.5/30
R-ATL: 10.0.0.6/30

---

## Routing Design

The branch routers use default routes toward HQ.

Example:

ip route 0.0.0.0 0.0.0.0 <HQ-next-hop>

HQ contains specific static routes toward branch LAN networks.

This allows HQ to operate as the central routing point between branch offices.

---

## DHCP

DHCP was configured on branch routers for employee networks.

Example:

ip dhcp excluded-address 192.168.220.1 192.168.220.20

ip dhcp pool ATL-EMPLOYEES
 network 192.168.220.0 255.255.255.0
 default-router 192.168.220.1
 dns-server 8.8.8.8

Infrastructure addresses were reserved while employee devices received addresses dynamically.

---

# Troubleshooting Scenarios

## INC-001 — Serial Encapsulation Mismatch

### Symptom
Atlanta could communicate locally but could not reach remote sites.

### Evidence
Serial interface:

up/down

### Diagnosis
Layer 1 was operational, but the Layer 2 line protocol was down.

R-ATL used PPP while R-HQ used HDLC.

### Resolution
Configured matching serial encapsulation.

### Verification
Interface returned to:

up/up

End-to-end connectivity was restored.

---

## INC-002 — Missing Default Route

### Symptom
Atlanta LAN worked locally but HQ and Chicago were unreachable.

### Diagnosis

show ip route

revealed that the branch default route was missing.

### Resolution

Restored the default route toward HQ.

### Result
Remote connectivity returned.

---

## INC-003 — DHCP Failure / Incorrect VLAN

### Symptom
ATL-PC1 could not obtain an IP address through DHCP.

### Investigation

show interfaces status
show vlan brief

### Root Cause
The employee access port was incorrectly assigned to VLAN 30 instead of VLAN 20.

### Resolution

interface fa0/1
 switchport access vlan 20

DHCP service was restored.

---

## INC-004 — VLAN Missing from Trunk

### Symptom
Employee VLAN lost connectivity while another VLAN continued operating.

### Investigation

show interfaces trunk

### Root Cause
VLAN 20 was missing from the trunk allowed VLAN list.

### Resolution

switchport trunk allowed vlan 20,30,99

---

## INC-005 — WAN Interface Administratively Down

### Symptom
Chicago users could communicate locally but could not reach remote sites.

### Investigation

show ip interface brief

### Evidence

Serial0/0/0 administratively down/down

### Resolution

interface serial0/0/0
 no shutdown

---

## INC-006 — Serial Layer-2 Failure

A serial encapsulation mismatch was used to reinforce Layer-2 WAN troubleshooting.

The interface displayed:

up/down

Detailed interface inspection revealed incompatible encapsulation between neighboring routers.

---

## INC-007 — Incorrect Static Route Next Hop

### Symptom
Chicago could reach HQ but not Atlanta.

### Investigation

show ip route

### Root Cause

Atlanta traffic was incorrectly routed toward the Chicago next hop.

### Resolution

The incorrect route was removed and replaced with the correct Atlanta next hop.

---

## INC-008 — Native VLAN Mismatch

### Symptom
HQ management connectivity failed even though the router subinterface remained up/up.

### Investigation

show ip interface brief
show interfaces trunk

### Root Cause

SW-HQ native VLAN: 10
R-HQ native VLAN: 99

### Resolution

Both sides were restored to native VLAN 99.

Management connectivity returned successfully.

---

# Verification Commands

show ip interface brief
show interfaces serial
show interfaces trunk
show vlan brief
show ip route
show ip dhcp pool
show ip dhcp binding
ping
traceroute

---

## Skills Demonstrated

- Enterprise WAN configuration
- Cisco IOS configuration
- Layer 2 troubleshooting
- Layer 3 troubleshooting
- VLAN and trunk troubleshooting
- DHCP troubleshooting
- Static route troubleshooting
- WAN fault isolation
- OSI-model-based troubleshooting
- Root-cause analysis
- End-to-end network validation

---

## Key Takeaway

The most important lesson from this project was learning to troubleshoot systematically rather than making random configuration changes.

The workflow used throughout the lab was:

Symptom
→ Verify local connectivity
→ Determine failure domain
→ Inspect interface state
→ Inspect Layer 2
→ Inspect Layer 3 routing
→ Identify root cause
→ Implement fix
→ Verify service restoration

---

## Lab Environment

- Cisco Packet Tracer
- Cisco IOS routers
- Cisco Layer 2 switches
- PCs
- Servers
- IP phones
- Serial WAN links
