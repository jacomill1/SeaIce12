# SeaIce Creamery Network Design Project

## Project Overview

This document provides a comprehensive summary of the SeaIce Creamery (SIC) Network Design project, capturing all key design decisions, specifications, and implementation details. This will serve as the foundational document for continuing the project.

## Table of Contents

1. [Project Background](https://claude.ai/chat/44f6bb17-ae84-405f-b765-2ad6d8b1d8b9#project-background)
2. [Phase 1: High-Level Design](https://claude.ai/chat/44f6bb17-ae84-405f-b765-2ad6d8b1d8b9#phase-1-high-level-design)
    - [Network Architecture](https://claude.ai/chat/44f6bb17-ae84-405f-b765-2ad6d8b1d8b9#network-architecture)
    - [IP Addressing Scheme](https://claude.ai/chat/44f6bb17-ae84-405f-b765-2ad6d8b1d8b9#ip-addressing-scheme)
    - [Bill of Materials](https://claude.ai/chat/44f6bb17-ae84-405f-b765-2ad6d8b1d8b9#bill-of-materials)
    - [Physical Connectivity](https://claude.ai/chat/44f6bb17-ae84-405f-b765-2ad6d8b1d8b9#physical-connectivity)
    - [Internet Connectivity Strategy](https://claude.ai/chat/44f6bb17-ae84-405f-b765-2ad6d8b1d8b9#internet-connectivity-strategy)
    - [Finance Department Security](https://claude.ai/chat/44f6bb17-ae84-405f-b765-2ad6d8b1d8b9#finance-department-security)
3. [Phase 2: Low-Level Design](https://claude.ai/chat/44f6bb17-ae84-405f-b765-2ad6d8b1d8b9#phase-2-low-level-design)
    - [Device Naming Convention](https://claude.ai/chat/44f6bb17-ae84-405f-b765-2ad6d8b1d8b9#device-naming-convention)
    - [Port Assignments](https://claude.ai/chat/44f6bb17-ae84-405f-b765-2ad6d8b1d8b9#port-assignments)
    - [VLAN Configuration](https://claude.ai/chat/44f6bb17-ae84-405f-b765-2ad6d8b1d8b9#vlan-configuration)
    - [Routing and Switching Protocols](https://claude.ai/chat/44f6bb17-ae84-405f-b765-2ad6d8b1d8b9#routing-and-switching-protocols)
    - [Wireless Network Design](https://claude.ai/chat/44f6bb17-ae84-405f-b765-2ad6d8b1d8b9#wireless-network-design)
4. [Reference Tables](https://claude.ai/chat/44f6bb17-ae84-405f-b765-2ad6d8b1d8b9#reference-tables)
    - [Network Zone Summary](https://claude.ai/chat/44f6bb17-ae84-405f-b765-2ad6d8b1d8b9#network-zone-summary)
    - [MDF IP Addressing](https://claude.ai/chat/44f6bb17-ae84-405f-b765-2ad6d8b1d8b9#mdf-ip-addressing)
    - [Virtual IP Assignments](https://claude.ai/chat/44f6bb17-ae84-405f-b765-2ad6d8b1d8b9#virtual-ip-assignments)
    - [Firewall Information](https://claude.ai/chat/44f6bb17-ae84-405f-b765-2ad6d8b1d8b9#firewall-information)

---

## Project Background

SeaIce Creamery (SIC) is expanding and moving its headquarters to a new location. The company needs technical assistance to design and plan the deployment of a wired and wireless LAN network that can support Voice over IP (VoIP) technologies. The project follows a three-phase approach:

1. **Phase 1**: Complete BoM and HLD (Focus on Wired LAN)
2. **Phase 2**: Complete LLD, present Wireless LAN Design, and lab out the design
3. **Phase 3**: Wired & Wireless LAN deployment, verification, and troubleshooting

Key requirements include:

- Network redundancy with no single point of failure
- Separate network zones for various services
- Special security considerations for the Finance Department
- Support for voice, data, and wireless communication
- Dual internet circuits with redundancy and failover
- Budget constraint of $250,000 for hardware

The network will use a collapsed core architecture with four IDFs and two MDFs.

---

## Phase 1: High-Level Design

### Network Architecture

SeaIce Creamery's network uses a **collapsed core architecture** with these key components:

1. **Core/Distribution Layer**:
    
    - Two high-performance Cisco Catalyst 9500 switches (one in each MDF)
    - Full redundancy between the two switches with port channels
    - Serves as both core and distribution layers in a single tier
2. **Access Layer**:
    
    - Cisco Catalyst 9300 series switches in each IDF
    - Dual uplinks to both core switches for redundancy
    - Special configuration for IDF2 (Finance Department)
3. **Security Boundaries**:
    
    - Palo Alto edge firewalls for internet security
    - Dedicated Palo Alto firewalls for Finance Department isolation
    - Transit VLAN (150) connecting firewalls to core network
4. **Wireless Infrastructure**:
    
    - Cisco 3504 Wireless Controllers (one in each MDF)
    - Cisco Aironet 3802i Access Points
    - Separate VLANs for corporate and guest wireless
5. **Network Services**:
    
    - DHCP, DNS, and Voice Gateway servers clustered across both MDFs
    - Financial Services Proxy servers in both MDFs
    - All services require Layer 2 adjacency for clustering

### IP Addressing Scheme

The network uses a 10.0.0.0/8 private address space with a structured approach where VLAN numbers correspond directly to the third octet in IP addresses:

|Network Zone|VLAN ID|Subnet|CIDR|Available IPs|Purpose|
|---|---|---|---|---|---|
|Management|250|10.0.250.0|/26|62|Network device management|
|Network Services|200|10.0.200.0|/26|62|DHCP, DNS, VG, FSP servers|
|SIC Data/Users|10|10.0.10.0|/23|510|General user data traffic|
|SIC Voice|20|10.0.20.0|/23|510|Voice traffic|
|SIC Wireless|32|10.0.32.0|/22|1022|Wireless client connectivity|
|SIC Guest Wireless|40|10.0.40.0|/22|1022|Guest wireless access|
|Finance Data/Users|100|10.0.100.0|/25|126|Finance department data|
|Finance Voice|110|10.0.110.0|/25|126|Finance department voice|
|FSP Transit|150|10.0.150.0|/29|6|FSP to Core transit network|
|Routing|254|10.0.254.0|/30|2|MDF-MDF routing|

### Bill of Materials

The project selected Option B (replacing ST fiber with LC) for better future-proofing. Key components include:

1. **Core Infrastructure**
    
    - 4 Cisco Catalyst 9500-24Y4C Core Switches
    - 4 Cisco Network Advantage Licenses (C9500)
    - 6 Cisco S-Class QSFP+ transceiver modules for core interconnection
2. **Edge Security**
    
    - 4 Palo Alto PA 820 Firewalls (2 for edge, 1 for Finance, 1 for HA)
    - 4 Palo Alto PA 820 Licenses
3. **Access Layer**
    
    - 13 Cisco Catalyst 9300-48P Switches
    - 13 Cisco Network Advantage Licenses (C9300)
4. **Wireless Infrastructure**
    
    - 2 Cisco 8500-L Wireless Controllers
    - 2 Cisco 8800 Power Supplies (Redundant)
    - 8 Cisco Aironet 3802i Access Points
    - 8 Cisco DNA Mobility Licenses (3-year)
    - 15 AP Mounting Kits
5. **Connectivity Components**
    
    - 32 Cisco SFP-10G-SR Transceivers
    - 16 OM4 Multimode LC-LC Fiber Cables
    - 2 OS2 Single-Mode LC-LC Fiber Cables
    - 1 Cisco 10GBASE SFP+ (for ST compatibility)
    - 500 Cat6 Patch Cables (various lengths)
6. **Power and Environmental**
    
    - 6 Tripp-Lite Smart 1050VA 650W UPS Tower units
    - Additional cable management accessories

Total budget allocation is approximately $230,000, staying within the project constraint of $250,000.

### Physical Connectivity

The physical connectivity design addresses legacy infrastructure considerations:

1. **MDF-to-MDF Connectivity**:
    
    - Primary: 40Gbps using QSFP+ over single-mode fiber
    - Redundant port channels with multiple physical links
    - Redundant paths for all traffic
2. **MDF-to-IDF Connectivity**:
    
    - 10Gbps connections using SFP-10G-SR transceivers
    - Dual uplinks from each IDF to both MDFs
    - Port channels for bandwidth aggregation and redundancy
3. **Fiber Infrastructure**:
    
    - Replacing legacy ST connectors with LC connectors
    - OM4 multimode fiber for IDF connections
    - OS2 single-mode fiber for MDF interconnection
4. **Internet Connectivity**:
    
    - Century Link: 100Mbps copper Ethernet to MDF1
    - Comcast: 100Mbps fiber to MDF2
    - Geographic redundancy for circuit protection

### Internet Connectivity Strategy

The dual internet strategy provides both redundancy and performance:

1. **Active/Active Configuration**:
    
    - Both circuits actively used simultaneously
    - Palo Alto firewalls manage traffic distribution
    - Policy-based routing for traffic optimization
2. **Edge Firewall Implementation**:
    
    - Palo Alto firewalls in active/passive configuration
    - Connected to both core switches for redundancy
    - HA links between firewalls for state synchronization
3. **Failover Mechanism**:
    
    - Automatic detection of circuit failures
    - Seamless rerouting of traffic to functioning circuit
    - BGP routing for efficient path selection

### Finance Department Security

The Finance Department requires special security isolation:

1. **Security Architecture**:
    
    - Dedicated Palo Alto firewalls in active/passive mode
    - Finance traffic isolated to VLAN 100 (Data) and 110 (Voice)
    - All Finance traffic must pass through FSP for inspection
2. **Transit Network**:
    
    - VLAN 150 serves as a security boundary
    - Connects Finance firewalls to core network
    - Facilitates clean traffic inspection and policy enforcement
3. **Traffic Flow**:
    
    - Finance devices use firewall as their default gateway
    - All traffic passes through inspection before reaching other networks
    - Return traffic follows the same path in reverse
4. **Implementation Strategy**:
    
    - Finance firewalls participate in OSPF routing
    - ACLs enforce traffic patterns
    - Security policies control permitted communications

---

## Phase 2: Low-Level Design

### Device Naming Convention

The network uses a structured naming convention for all devices:

```
SIC-[LOCATION]-[DEVICE TYPE]-[NUMBER]
```

Examples:

- **SIC-MDF1-CORE-01**: Core switch in MDF1
- **SIC-IDF2-ACC-01**: Access switch in IDF2
- **SIC-MDF1-FW-01**: Edge firewall in MDF1
- **SIC-MDF1-FNFW-01**: Finance firewall in MDF1
- **SIC-IDF3-AP-02**: Access point #2 in IDF3 area

### Port Assignments

#### Edge Firewalls

**SIC-MDF1-FW-01 (Primary Edge Firewall)**

|Interface|Connected To|Purpose|VLAN/Subnet|Notes|
|---|---|---|---|---|
|eth1/1|Century Link|WAN Connection|ISP Assigned|Primary Internet|
|eth1/2|SIC-MDF1-CORE-01 (Te1/0/1)|Core Connectivity|VLAN 150 (10.0.150.5/29)|Transit network|
|eth1/3|SIC-MDF2-CORE-01 (Te1/0/1)|Redundant Core Link|VLAN 150 (10.0.150.5/29)|Redundancy|
|eth1/4|SIC-MDF2-FW-01 eth1/4|HA1 Link|N/A|Control plane sync|
|eth1/5|SIC-MDF2-FW-01 eth1/5|HA2 Link|N/A|Session state sync|
|MGT|Management Network|OOB Management|VLAN 250 (10.0.250.11/26)|Management|

**SIC-MDF2-FW-01 (Secondary Edge Firewall)**

|Interface|Connected To|Purpose|VLAN/Subnet|Notes|
|---|---|---|---|---|
|eth1/1|Comcast|WAN Connection|ISP Assigned|Secondary Internet|
|eth1/2|SIC-MDF2-CORE-01 (Te1/0/1)|Core Connectivity|VLAN 150 (10.0.150.6/29)|Transit network|
|eth1/3|SIC-MDF1-CORE-01 (Te1/0/2)|Redundant Core Link|VLAN 150 (10.0.150.6/29)|Redundancy|
|eth1/4|SIC-MDF1-FW-01 eth1/4|HA1 Link|N/A|Control plane sync|
|eth1/5|SIC-MDF1-FW-01 eth1/5|HA2 Link|N/A|Session state sync|
|MGT|Management Network|OOB Management|VLAN 250 (10.0.250.12/26)|Management|

#### Core Switches

**SIC-MDF1-CORE-01**

|Interface|Connected To|Purpose|VLAN/Subnet|Configuration|
|---|---|---|---|---|
|Te1/0/1|SIC-MDF1-FW-01 eth1/2|Edge Firewall|VLAN 150|Trunk (VLAN 150)|
|Te1/0/2|SIC-MDF2-FW-01 eth1/3|Redundant Edge FW|VLAN 150|Trunk (VLAN 150)|
|Te1/0/3|SIC-MDF1-FNFW-01 eth1/2|Finance FW|VLAN 150|Trunk (VLAN 150)|
|Te1/0/4|SIC-MDF2-FNFW-01 eth1/3|Redundant Finance FW|VLAN 150|Trunk (VLAN 150)|
|Te1/0/5-8|SIC-MDF2-CORE-01 Te1/0/5-8|Core Interconnect|All VLANs|Trunk (All VLANs)|
|Te1/0/9-10|SIC-IDF1-ACC-01 Te1/0/1-2|IDF1 Uplink|User VLANs|Trunk (VLANs 10,20,32,40,250)|
|Te1/0/11-12|SIC-IDF2-ACC-01 Te1/0/1-2|IDF2 Uplink|Finance VLANs|Trunk (VLANs 100,110,250)|
|Te1/0/13-14|SIC-IDF3-ACC-01 Te1/0/1-2|IDF3 Uplink|User VLANs|Trunk (VLANs 10,20,32,40,250)|
|Te1/0/15-16|SIC-IDF4-ACC-01 Te1/0/1-2|IDF4 Uplink|User VLANs|Trunk (VLANs 10,20,32,40,250)|
|Gi1/0/1-4|Network Service Servers|Services|VLAN 200|Access (VLAN 200)|
|Gi1/0/5|SIC-MDF1-WLC-01|Wireless Controller|VLAN 32|Trunk (VLANs 32,40,250)|
|Gi1/0/24|Management Network|OOB Management|VLAN 250|Access (VLAN 250)|

**SIC-MDF2-CORE-01**

|Interface|Connected To|Purpose|VLAN/Subnet|Configuration|
|---|---|---|---|---|
|Te1/0/1|SIC-MDF2-FW-01 eth1/2|Edge Firewall|VLAN 150|Trunk (VLAN 150)|
|Te1/0/2|SIC-MDF1-FW-01 eth1/3|Redundant Edge FW|VLAN 150|Trunk (VLAN 150)|
|Te1/0/3|SIC-MDF2-FNFW-01 eth1/2|Finance FW|VLAN 150|Trunk (VLAN 150)|
|Te1/0/4|SIC-MDF1-FNFW-01 eth1/3|Redundant Finance FW|VLAN 150|Trunk (VLAN 150)|
|Te1/0/5-8|SIC-MDF1-CORE-01 Te1/0/5-8|Core Interconnect|All VLANs|Trunk (All VLANs)|
|Te1/0/9-10|SIC-IDF1-ACC-01 Te1/0/3-4|IDF1 Uplink|User VLANs|Trunk (VLANs 10,20,32,40,250)|
|Te1/0/11-12|SIC-IDF2-ACC-01 Te1/0/3-4|IDF2 Uplink|Finance VLANs|Trunk (VLANs 100,110,250)|
|Te1/0/13-14|SIC-IDF3-ACC-01 Te1/0/3-4|IDF3 Uplink|User VLANs|Trunk (VLANs 10,20,32,40,250)|
|Te1/0/15-16|SIC-IDF4-ACC-01 Te1/0/3-4|IDF4 Uplink|User VLANs|Trunk (VLANs 10,20,32,40,250)|
|Gi1/0/1-4|Network Service Servers|Services|VLAN 200|Access (VLAN 200)|
|Gi1/0/5|SIC-MDF2-WLC-01|Wireless Controller|VLAN 32|Trunk (VLANs 32,40,250)|
|Gi1/0/24|Management Network|OOB Management|VLAN 250|Access (VLAN 250)|

#### Finance Firewalls

**SIC-MDF1-FNFW-01 (Primary Finance Firewall)**

|Interface|Connected To|Purpose|VLAN/Subnet|Configuration|
|---|---|---|---|---|
|eth1/1.100|SIC-IDF2-ACC-01|Finance Data Inside|VLAN 100 (10.0.100.5/25)|Subinterface VLAN 100|
|eth1/1.110|SIC-IDF2-ACC-01|Finance Voice Inside|VLAN 110 (10.0.110.5/25)|Subinterface VLAN 110|
|eth1/2|SIC-MDF1-CORE-01 Te1/0/3|Outside Interface|VLAN 150 (10.0.150.5/29)|Trunk (VLAN 150)|
|eth1/3|SIC-MDF2-CORE-01 Te1/0/3|Redundant Outside|VLAN 150 (10.0.150.5/29)|Trunk (VLAN 150)|
|eth1/4|SIC-MDF2-FNFW-01 eth1/4|HA1 Link|N/A|Control plane sync|
|eth1/5|SIC-MDF2-FNFW-01 eth1/5|HA2 Link|N/A|Session state sync|
|MGT|Management Network|OOB Management|VLAN 250 (10.0.250.21/26)|Management|

**SIC-MDF2-FNFW-01 (Secondary Finance Firewall)**

|Interface|Connected To|Purpose|VLAN/Subnet|Configuration|
|---|---|---|---|---|
|eth1/1.100|SIC-IDF2-ACC-01|Finance Data Inside|VLAN 100 (10.0.100.6/25)|Subinterface VLAN 100|
|eth1/1.110|SIC-IDF2-ACC-01|Finance Voice Inside|VLAN 110 (10.0.110.6/25)|Subinterface VLAN 110|
|eth1/2|SIC-MDF2-CORE-01 Te1/0/3|Outside Interface|VLAN 150 (10.0.150.6/29)|Trunk (VLAN 150)|
|eth1/3|SIC-MDF1-CORE-01 Te1/0/4|Redundant Outside|VLAN 150 (10.0.150.6/29)|Trunk (VLAN 150)|
|eth1/4|SIC-MDF1-FNFW-01 eth1/4|HA1 Link|N/A|Control plane sync|
|eth1/5|SIC-MDF1-FNFW-01 eth1/5|HA2 Link|N/A|Session state sync|
|MGT|Management Network|OOB Management|VLAN 250 (10.0.250.22/26)|Management|

### VLAN Configuration

(This section will be populated as we continue the LLD development)

### Routing and Switching Protocols

The network uses several key protocols for routing and redundancy:

1. **OSPF (Open Shortest Path First)**:
    
    - Used between core switches and firewalls
    - All devices participate in Area 0
    - Provides dynamic routing and automatic failover
    - Advertises appropriate networks between security zones
2. **Rapid PVST+ (Per-VLAN Spanning Tree)**:
    
    - Used for Layer 2 loop prevention
    - Each VLAN has its own spanning tree instance
    - Root bridge assignments align with VRRP primary devices
    - MDF1 primary for VLANs 10, 32, 100, 200
    - MDF2 primary for VLANs 20, 40, 110, 250
3. **VRRP (Virtual Router Redundancy Protocol)**:
    
    - Used for gateway redundancy on core switches
    - Virtual IP (VIP) serves as the default gateway
    - Primary/secondary roles distributed across core switches
    - Fast failover if primary device fails
4. **Palo Alto High Availability**:
    
    - Native active/passive configuration for firewall pairs
    - Complete configuration synchronization
    - Session state synchronization for seamless failover
    - Dedicated HA1 and HA2 links between firewall pairs
5. **Port Channels**:
    
    - LACP (Link Aggregation Control Protocol) for all multi-link connections
    - Provides bandwidth aggregation and link redundancy
    - Used for all critical links in the network

### Wireless Network Design

(This section will be populated as we develop the wireless design)

---

## Reference Tables

### Network Zone Summary

|Network Zone|VLAN ID|Subnet|CIDR|Default Gateway|Primary Router|
|---|---|---|---|---|---|
|Management|250|10.0.250.0|/26|10.0.250.1|MDF2|
|Network Services|200|10.0.200.0|/26|10.0.200.1|MDF1|
|SIC Data/Users|10|10.0.10.0|/23|10.0.10.1|MDF1|
|SIC Voice|20|10.0.20.0|/23|10.0.20.1|MDF2|
|SIC Wireless|32|10.0.32.0|/22|10.0.32.1|MDF1|
|SIC Guest Wireless|40|10.0.40.0|/22|10.0.40.1|MDF2|
|Finance Data/Users|100|10.0.100.0|/25|10.0.100.1|FNFW1|
|Finance Voice|110|10.0.110.0|/25|10.0.110.1|FNFW1|
|FSP Transit|150|10.0.150.0|/29|10.0.150.1|N/A|
|Routing|254|10.0.254.0|/30|N/A|N/A|

### MDF IP Addressing

**MDF1 Core Switch (SIC-MDF1-CORE-01)**

|Interface|VLAN|IP Address|Subnet Mask|Default Gateway|
|---|---|---|---|---|
|VLAN 10|10|10.0.10.2|255.255.254.0|N/A|
|VLAN 20|20|10.0.20.2|255.255.254.0|N/A|
|VLAN 32|32|10.0.32.2|255.255.252.0|N/A|
|VLAN 40|40|10.0.40.2|255.255.252.0|N/A|
|VLAN 100|100|10.0.100.2|255.255.255.128|N/A|
|VLAN 110|110|10.0.110.2|255.255.255.128|N/A|
|VLAN 150|150|10.0.150.2|255.255.255.248|N/A|
|VLAN 200|200|10.0.200.2|255.255.255.192|N/A|
|VLAN 250|250|10.0.250.2|255.255.255.192|N/A|
|VLAN 254|254|10.0.254.2|255.255.255.252|N/A|

**MDF2 Core Switch (SIC-MDF2-CORE-01)**

|Interface|VLAN|IP Address|Subnet Mask|Default Gateway|
|---|---|---|---|---|
|VLAN 10|10|10.0.10.3|255.255.254.0|N/A|
|VLAN 20|20|10.0.20.3|255.255.254.0|N/A|
|VLAN 32|32|10.0.32.3|255.255.252.0|N/A|
|VLAN 40|40|10.0.40.3|255.255.252.0|N/A|
|VLAN 100|100|10.0.100.3|255.255.255.128|N/A|
|VLAN 110|110|10.0.110.3|255.255.255.128|N/A|
|VLAN 150|150|10.0.150.3|255.255.255.248|N/A|
|VLAN 200|200|10.0.200.3|255.255.255.192|N/A|
|VLAN 250|250|10.0.250.3|255.255.255.192|N/A|
|VLAN 254|254|10.0.254.3|255.255.255.252|N/A|

### Virtual IP Assignments

|Network Zone|VLAN ID|Virtual IP|Purpose|
|---|---|---|---|
|Management|250|10.0.250.1|Management default gateway|
|Network Services|200|10.0.200.1|Services default gateway|
|SIC Data/Users|10|10.0.10.1|User default gateway|
|SIC Voice|20|10.0.20.1|Voice default gateway|
|SIC Wireless|32|10.0.32.1|Wireless default gateway|
|SIC Guest Wireless|40|10.0.40.1|Guest wireless default gateway|
|Finance Data/Users|100|10.0.100.1|Finance data gateway (firewall)|
|Finance Voice|110|10.0.110.1|Finance voice gateway (firewall)|
|FSP Transit|150|10.0.150.1|Core transit gateway|
|FSP Outside VIP|150|10.0.150.4|FSP next-hop address|

### Firewall Information

**Palo Alto Active/Passive Configuration**

|Firewall Pair|Active Unit|Passive Unit|HA Method|Purpose|
|---|---|---|---|---|
|Edge Firewalls|SIC-MDF1-FW-01|SIC-MDF2-FW-01|Active/Passive|Internet security|
|Finance Firewalls|SIC-MDF1-FNFW-01|SIC-MDF2-FNFW-01|Active/Passive|Finance isolation|

**Virtual Addresses (Floating IPs)**

|Service|Virtual IP|Actual IPs|Purpose|
|---|---|---|---|
|Edge Firewall Outside|ISP Assigned|N/A|Internet connectivity|
|Edge Firewall Inside|10.0.150.5|10.0.150.5, 10.0.150.6|Core connectivity|
|Finance Firewall Inside|10.0.100.1, 10.0.110.1|10.0.100.5/6, 10.0.110.5/6|Finance gateway|
|Finance Firewall Outside|10.0.150.4|10.0.150.5, 10.0.150.6|Core connectivity|

---

This document serves as a comprehensive overview of the SeaIce Creamery Network Design Project through the beginning of Phase 2. As the project progresses, additional sections will be completed and details refined.

#claude