# SeaIce Creamery Network Project Overview

## Quick Facts
- **Budget**: $250,000 (excluding professional services)
- **Timeline**: 3 weeks total
- **Project Number**: AST-SIC0098
- **Vendors**: Must use Insight and eBay for equipment
- **Hardware Requirement**: Cisco appliances only for wired LAN
- **Available IP Space**: 10.0.0.0/8

## Core Requirements

### Network Zones (Minimum 6 Required)
1. Network Services
2. Campus Data
3. Campus Voice
4. Campus Wireless
5. Finance Data
6. Finance Voice

### Special Considerations
#### Finance Department (FD)
- Located in IDF2
- Wired connections only
- Must be isolated from rest of network
- Can only communicate with FSP servers
- Requires special traffic flow: FD → FSP → Services/Internet

#### Network Services
Each MDF requires:
- DHCP server
- DNS server
- Voice Gateway (VG)
- FSP servers
- Must be L2 adjacent for clustering
- Each service needs 3 IPs (2 physical + 1 VIP)

#### Internet Connectivity
- Two circuits (one to each MDF)
- Both used in production
- Must provide failover capability
- No single point of failure allowed

## Project Phases

### Phase 1: Basic Design
- Create High Level Design (HLD)
- Develop Bill of Materials (BoM)
- Focus on Layer 1 connectivity
- Plan traffic flow strategy
- Consider redundancy requirements

### Phase 2: Detailed Design
- Create Low Level Design (LLD)
- Design wireless LAN
- Develop detailed IP addressing scheme
- Lab testing begins
- Detail all traffic flows and security measures

### Phase 3: Implementation
- Create Maintenance Operating Procedure (MOP)
- Deploy wired and wireless networks
- Implement all security measures
- Document troubleshooting procedures
- Verify all features work as designed

## Limitations
- Using existing building cabling
- No new cable runs between closets
- Must use Cisco appliances
- Hardware outside MDFs/IDFs not included (except APs)

## Key Deliverables
1. Bill of Materials (BoM)
2. IP addressing scheme
3. High Level Design (HLD)
4. Low Level Design (LLD)
5. Maintenance Operating Procedure (MOP)
6. Working lab topology
7. Implementation and testing plans
8. Configuration scripts for all devices

## Documentation Requirements
### High Level Design Must Include
- Layer 1 connectivity details
- General traffic flow strategy
- Network services access plan
- Internet circuit connectivity plan
- Finance Department isolation strategy
- Redundancy considerations
- Subnet size calculations

### Low Level Design Must Include
- L2/L3 connectivity details
- Specific features/technologies per device
- Complete IP addressing scheme
- Code level recommendations
- Network services configuration details
- Finance Department isolation implementation
- Full redundancy implementation

### MOP Must Include
- Configuration scripts
- Implementation procedures
- Testing plans
- Troubleshooting procedures
- Protocol-specific testing methods

## Tags
#network-design #cisco #enterprise-network #project-requirements #statement-of-work #SIC 