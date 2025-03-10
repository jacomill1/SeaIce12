# Phase 1: High Level Design & Bill of Materials
## Task Overview
Primary deliverables for this phase are:
1. High Level Design (HLD) document
2. Bill of Materials (BoM)
3. Initial network strategy documentation

## High Level Design Requirements

### 1. Physical Layout Documentation
- Map out physical connections between:
  - MDFs (Main Distribution Frames)
  - IDF2 (Finance Department)
  - Network closets
  - Access point locations
- Document existing cable runs
- Identify redundant path requirements

### 2. Core Network Strategy
Must address these key points:
- Traffic flow patterns for:
  - Campus Data & Voice
  - Finance Department Data & Voice
  - Network services accessibility
  - Internet circuit utilization
- Finance Department isolation approach
- Redundancy implementation strategy
- Initial subnet size calculations

### 3. Network Services Planning
Document connection strategy for:
- DHCP servers (2 physical + 1 VIP)
- DNS servers (2 physical + 1 VIP)
- Voice Gateways (2 physical + 1 VIP)
- FSP servers
- L2 adjacency requirements for clustering

## Bill of Materials Requirements

### 1. Hardware Components
Must include:
- Switches
- Routers
- Wireless controllers
- Access points
- Transceivers
- Patch cables
- Spare components (10-15% extra)

### 2. Vendor Requirements
- Primary vendor: Insight (www.insight.com)
- Secondary vendor: eBay (www.ebay.com)
- Must use Cisco appliances for wired LAN
- Stay within $250,000 budget

### 3. Specific Considerations
- Include only hardware for MDFs/IDFs
- Exception: Wireless access points
- Must account for redundancy
- Include spares for critical components

## Required Documentation

### 1. Layer 1 Design Document
Must include:
- Physical topology diagram
- Cable run documentation
- Device placement strategy
- Redundancy paths

### 2. Network Strategy Document
Must outline:
- Basic traffic flow patterns
- Initial security zones
- Preliminary IP scheme thoughts
- Internet circuit usage plan

### 3. Hardware Requirements Document
Should detail:
- Equipment list with quantities
- Cost breakdown
- Vendor sources
- Spare parts strategy

## **Success Criteria**
To complete Phase 1, must have:
1. Complete physical network diagram
2. Detailed equipment list with costs
3. Initial network strategy document
4. Preliminary security zone planning
5. Basic redundancy planning
6. Internet circuit strategy
7. Finance Department isolation strategy

## Dependencies
- Network Floor Diagram from SIC
- Access to existing cable run documentation
- Input from SIC IT staff regarding:
  - Current network services configuration
  - FSP requirements
  - Voice gateway needs

## Next Steps
After Phase 1 completion:
1. Review documentation with SIC staff
2. Validate physical layout plans
3. Confirm budget alignment
4. Begin detailed Layer 2/3 planning
5. Start lab environment preparation

## Notes
- Keep detailed notes of all assumptions
- Document any limitations identified
- Note areas needing clarification
- Track any potential risks or issues

# Tags 

#phase1 #network-design #high-level-design #bill-of-materials


## Project Structure Tags

- #SIC/Phase1
- #SIC/HLD
- #SIC/BoM
- #SIC/Documentation

## Network Design Tags

- #SIC/CollapsedCore
- #SIC/Layer1
- #SIC/Cabling
- #SIC/FiberUpgrade
- #SIC/PhysicalTopology
- #SIC/VLANs
- #SIC/Subnetting
- #SIC/IPAddressing
- #SIC/Redundancy
- #SIC/Transceivers

## Security Tags

- #SIC/FinanceSecurity
- #SIC/FirewallDesign
- #SIC/SecurityZones
- #SIC/TrafficSegmentation
- #SIC/PaloAlto

## Infrastructure Tags

- #SIC/MDF
- #SIC/IDF
- #SIC/EdgeFirewalls
- #SIC/CoreSwitches
- #SIC/AccessSwitches
- #SIC/WirelessController
- #SIC/AccessPoints

## Budget Tags

- #SIC/Budget
- #SIC/CostAnalysis
- #SIC/OptionA
- #SIC/OptionB

## Protocol Tags

- #SIC/OSPF
- #SIC/STP
- #SIC/HSRP
- #SIC/BGP
- #SIC/PortChannel

## Service Tags

- #SIC/NetworkServices
- #SIC/DHCP
- #SIC/DNS
- #SIC/VoiceGateway
- #SIC/FSP


## Core Networking Concepts

- #networking
- #routing
- #switching
- #security
- #wireless
- #datacenter
- #wan
- #lan
- #infrastructure
- #architecture

## Specific Protocols

- #ospf
- #bgp
- #eigrp
- #isis
- #stp
- #vlan
- #dhcp
- #dns
- #vpn
- #ipv4
- #ipv6
- #mpls
- #qos
- #nat

## Network Design Elements

- #redundancy
- #highavailability
- #performance
- #scalability
- #segmentation
- #firewall
- #loadbalancing
- #addressing
- #subnetting
- #bandwidth