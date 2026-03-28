# Dynamic-Routing-using-RIP-protocol
# Practical Report: Dynamic Routing using RIP & DHCP Configuration

## 1. Aim
To configure Dynamic Routing using the RIP protocol and DHCP services in a multi-router network and verify end-to-end connectivity between LAN1 and LAN2.

## 2. Topology Description
The network consists of:
- **Routers (2811):** 4
- **Switches:** 2
- **PCs:** 2
- **Laptop:** 1
- **Server:** 1

Two LANs are connected through 4 routers forming a ring topology using RIP routing.

## 3. IP Addressing Scheme
### Major Network
We subnetted `10.0.0.0/8` into 8 subnets (`/11`).

| Network | Used For |
| :--- | :--- |
| `10.0.0.0/11` | LAN1 |
| `10.32.0.0/11` | R0 – R1 |
| `10.64.0.0/11` | R1 – R2 |
| `10.96.0.0/11` | LAN2 |
| `10.128.0.0/11` | R2 – R3 |
| `10.160.0.0/11` | R3 – R4 |
| `10.192.0.0/11` | R4 – R0 |

### Interface Address Table

**Router0**
| Interface | IP Address |
| :--- | :--- |
| Fa1/0 (LAN1) | `10.0.0.1` |
| Fa0/0 → R1 | `10.32.0.1` |
| Fa0/1 → R4 | `10.192.0.1` |

**Router1**
| Interface | IP Address |
| :--- | :--- |
| Fa0/1 → R0 | `10.32.0.2` |
| Fa0/0 → R2 | `10.64.0.1` |

**Router2**
| Interface | IP Address |
| :--- | :--- |
| Fa0/1 → R1 | `10.64.0.2` |
| Fa1/0 (LAN2) | `10.96.0.1` |
| Fa0/0 → R3 | `10.128.0.1` |

**Router3**
| Interface | IP Address |
| :--- | :--- |
| Fa0/1 → R2 | `10.128.0.2` |
| Fa0/0 → R4 | `10.160.0.1` |

**Router4**
| Interface | IP Address |
| :--- | :--- |
| Fa0/1 → R0 | `10.192.0.2` |
| Fa0/0 → R3 | `10.160.0.2` |

## 4. End Devices
| Device | IP Assignment | Gateway |
| :--- | :--- | :--- |
| PC0 | DHCP | `10.0.0.1` |
| PC1 | DHCP | `10.0.0.1` |
| Laptop | DHCP | `10.96.0.1` |
| Server | DHCP | `10.96.0.1` |

## 5. Basic Router Configuration (ALL ROUTERS)
```text
enable
configure terminal
```

## 6. Router Interface Configuration
### Router0
```text
conf t
interface fa1/0
ip address 10.0.0.1 255.224.0.0
no shutdown

interface fa0/0
ip address 10.32.0.1 255.224.0.0
no shutdown

interface fa0/1
ip address 10.192.0.1 255.224.0.0
no shutdown
```

### Router1
```text
conf t
interface fa0/1
ip address 10.32.0.2 255.224.0.0
no shutdown

interface fa0/0
ip address 10.64.0.1 255.224.0.0
no shutdown
```

### Router2
```text
conf t
interface fa0/1
ip address 10.64.0.2 255.224.0.0
no shutdown

interface fa1/0
ip address 10.96.0.1 255.224.0.0
no shutdown

interface fa0/0
ip address 10.128.0.1 255.224.0.0
no shutdown
```

### Router3
```text
conf t
interface fa0/1
ip address 10.128.0.2 255.224.0.0
no shutdown

interface fa0/0
ip address 10.160.0.1 255.224.0.0
no shutdown
```

### Router4
```text
conf t
interface fa0/1
ip address 10.192.0.2 255.224.0.0
no shutdown

interface fa0/0
ip address 10.160.0.2 255.224.0.0
no shutdown
```

## 7. RIP Dynamic Routing Configuration

### 🟢 Router0 – RIP Configuration
*Networks connected to R0:*
- 10.0.0.0/11 
- 10.32.0.0/11 
- 10.192.0.0/11 

```text
router rip
version 2
network 10.0.0.0
network 10.32.0.0
network 10.192.0.0


### 🟢 Router1 – RIP Configuration
*Networks connected:*
- 10.32.0.0/11 (R0)
- 10.64.0.0/11 (R2)

```text
router rip
version 2
no auto-summary
network 10.32.0.0
network 10.64.0.0

### 🟢 Router2 – RIP Configuration
*Networks connected:*
- 10.64.0.0/11 (R1)
- 10.96.0.0/11 (LAN2)
- 10.128.0.0/11 (R3)

```text
router rip
version 2
no auto-summary
network 10.64.0.0
network 10.96.0.0
network 10.128.0.0

### 🟢 Router3 – RIP Configuration
*Networks connected:*
- 10.128.0.0/11 (R2)
- 10.160.0.0/11 (R4)

```text
router rip
version 2
no auto-summary
network 10.128.0.0
network 10.160.0.0


### 🟢 Router4 – RIP Configuration
*Networks connected:*
- 10.192.0.0/11 (R0)
- 10.160.0.0/11 (R3)

```text
router rip
version 2
no auto-summary
network 10.160.0.0
network 10.192.0.0


## 8. DHCP Configuration
We configure DHCP on routers connected to LANs.

### DHCP on Router0 (LAN1)

ip dhcp pool P1
network 10.0.0.0 255.224.0.0
default-router 10.0.0.1

### DHCP on Router2 (LAN2)


ip dhcp pool P2
network 10.96.0.0 255.224.0.0
default-router 10.96.0.1


## 9. Configure End Devices
On PCs/Laptop/Server (within Cisco Packet Tracer or similar emulator):
* Navigate to **Desktop** → **IP Configuration** → **Select DHCP**
* Devices will automatically receive IP addresses.

## 10. Verification Commands
Run on routers to verify the routing table and protocols:
```text
show ip route
debug ip rip
show ip protocols
```
**Expected Result:**
* All routers learn routes dynamically via RIP.
* Routes are marked with `R` in the routing table.

## 11. Connectivity Testing
**From PC0:**
```text
ping 10.96.0.2   (to Laptop)
ping 10.96.0.3   (to Server)
```

**From Laptop:**
```text
ping 10.0.0.2
ping 10.0.0.3
```
> *All pings should be successful, verifying end-to-end connectivity across the network.*

## 12. Final Result
* RIP dynamic routing was successfully configured.
* DHCP automatically assigned IP addresses to all hosts.
* Full connectivity achieved between LAN1 and LAN2.

## 13. Conclusion
This experiment demonstrates how RIP simplifies routing by automatically sharing routes between multiple routers, and how DHCP reduces manual configuration by automatically assigning IP addresses to end devices, thus making large networks easier to manage and scale.
