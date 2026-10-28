# static-routing-floating-static-route-redundancy-architecture
Multi-hop static routing topology consisting of 5 Cisco 2911 integrated services routers (R2, R3, R4, R5, R6) arranged in a primary and backup path configuration to guarantee deterministic reachability without relying on dynamic routing protocols.

🌐 Quick Overview & Network Specs🟢 Architecture & Topology: Multi-hop static routing topology consisting of 5 Cisco 2911 integrated services routers (R2, R3, R4, R5, R6) arranged in a primary and backup path configuration to guarantee deterministic reachability without relying on dynamic routing protocols.   🔵 Inter-Router Subnet Allocations:R2 ↔ R3 Transit Link: Subnet 23.0.0.0/24   R3 ↔ R4 Transit Link: Subnet 34.0.0.0/24   R2 ↔ R6 Transit Link (Backup Path): Subnet 26.0.0.0/24   R6 ↔ R4 Transit Link (Backup Path): Subnet 46.0.0.0/24   R4 ↔ R5 Access Link: Subnet 45.0.0.0/24   🟣 Primary vs. Backup Path Design:Primary Forwarding Path: Traffic from R2 destined to R5 routes through R3 (R2 $\rightarrow$ R3 $\rightarrow$ R4 $\rightarrow$ R5) using standard static routes with default Administrative Distance (AD = 1).   Floating Static Backup Path: Traffic routes through R6 (R2 $\rightarrow$ R6 $\rightarrow$ R4 $\rightarrow$ R5) only as a backup by assigning a higher Administrative Distance (e.g., AD = 10 or 130).   💡 Key Technical Insights & What This Lab Demonstrates🔍 Pure Static Path Engineering: Proves complete mastery of manual IPv4 route table manipulation without relying on dynamic routing protocols (OSPF, EIGRP, or BGP).   ⚡ Floating Static Route Redundancy: Demonstrates how assigning a higher Administrative Distance (AD > 1) to static routes via R6 ensures they remain inactive in the RIB (Routing Information Base) until the primary path via R3 fails.   🛠️ Recursive Route Resolution & Next-Hop Design: Illustrates proper next-hop IP addressing configuration across multi-hop topologies to prevent routing loops and asymmetric paths.   🛡️ Path Failover & ICMP Verification: Validates automatic failover convergence times when primary links (23.0.0.0/24 or 34.0.0.0/24) are shut down.   🛠️ Essential Cisco CLI Configuration SnippetsRouter R2 Configuration (Primary via R3, Floating Backup via R6)Cisco CLI! Primary Static Route to R5 via R3 (AD = 1 by default)
ip route 45.0.0.0 255.255.255.0 23.0.0.3

! Floating Static Route to R5 via R6 (AD = 10 for Backup)
ip route 45.0.0.0 255.255.255.0 26.0.0.6 10
Router R3 Configuration (Primary Intermediate Transit)Cisco CLIip route 45.0.0.0 255.255.255.0 34.0.0.4
ip route 26.0.0.0 255.255.255.0 23.0.0.2
Router R6 Configuration (Backup Transit Path)Cisco CLIip route 45.0.0.0 255.255.255.0 46.0.0.4
ip route 23.0.0.0 255.255.255.0 26.0.0.2
Router R4 & R5 Return Routing ConfigurationsCisco CLI! On R4
ip route 23.0.0.0 255.255.255.0 34.0.0.3
ip route 26.0.0.0 255.255.255.0 46.0.0.6 10

! On R5
ip route 0.0.0.0 0.0.0.0 45.0.0.4
🧪 Verification & Audit CommandsBash# Verify active routes in the IPv4 Routing Information Base (RIB)
show ip route
show ip route static

# Confirm active next-hop selection and administrative distance
show ip route 45.0.0.5

# Test end-to-end reachability and verify forwarding path hops
ping 45.0.0.5
traceroute 45.0.0.5
