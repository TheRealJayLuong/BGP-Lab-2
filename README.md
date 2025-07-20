# BGP Lab 2 – Route Reflection and Path Control
## 🧭 Lab Overview

This lab explores **Border Gateway Protocol (BGP)** deployment in Cisco IOS routers, emphasizing:
- Internal BGP (iBGP) configuration
- Route reflection within the same cluster
- Community-based path control using well-known attributes
- Preference enforcement through BGP attributes like LOCAL_PREF

The topology consists of four routers (R1–R4) interconnected via point-to-point links, with each router connected to a unique LAN subnet and configured with a Loopback 0 interface for router ID assignment and peering.

## 🌐 Network Topology

- **AS Number**: 254 (single autonomous system)
- **Routing Protocols**:
  - EIGRP for internal reachability (AS 254), excluding LAN subnets
  - iBGP between Loopback interfaces
- **IP Schema**:
  - Point-to-point links: `10.0.0.0/30`, `10.0.0.4/30`, `10.0.0.12/30`
  - Loopbacks: `1.1.1.1`, `2.2.2.2`, `3.3.3.3`, `4.4.4.4`
  - LANs: `150.1.1.0/24`, `150.2.2.0/24`, `150.3.3.0/24`, `150.4.4.0/24`

## ⚙️ Key Implementation Methods

### 1. **Route Reflection**
- R1 and R3 configured as route reflectors within the same cluster (`cluster-id 55.55.55.55`)
- R2 and R4 are RR clients of both R1 and R3
- Avoids full mesh by allowing reflected updates
- Prevents loops using `ORIGINATOR_ID` and `CLUSTER_LIST`

### 2. **BGP Peer Configuration**
- All iBGP sessions use Loopback 0 IP addresses
- `peer-groups` implemented on R2 and R4 for simplified neighbor configurations
- MD5 authentication enabled (`password CCNP`)
- Aggressive timers: `hello 5s`, `hold 15s` to test convergence behavior

### 3. **Community Attribute Control**
- LAN subnets redistributed into BGP using route-maps
- All interfaces matched via `match interface` for connected routes
- `local-as` community tag assigned to prevent eBGP advertisement
- Peers enabled to send/receive community attributes (`send-community standard`)

### 4. **Path Preference via LOCAL_PREF**
- R3 adjusts `LOCAL_PREF` to 200 for routes received from R2 and R4
- Ensures R2 and R4 prefer paths via R3 instead of R1
- Configured inbound on R3 and propagates internally via iBGP

## ✅ Verification Highlights

- Use `show ip bgp summary` to confirm session states
- Use `show ip bgp <prefix>` to confirm community attributes and best path selection
- BGP routes with community `local-AS` are retained within AS 254
- Verify preferred path via LOCAL_PREF with `show ip bgp` output comparing values