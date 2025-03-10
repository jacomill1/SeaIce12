# SVI-Based Network Design: Explanation and Defense

#tags: #network #design #SVI #layer3 #LLD #presentations

## Overview of Our Layer 3 Design Approach

Our Sea Ice Creamery network uses a Switched Virtual Interface (SVI) design philosophy rather than physical or logical subinterfaces for establishing Layer 3 boundaries. This represents an enterprise-grade approach that centralizes routing functions on VLAN interfaces rather than physical ports.

### SVI-Based vs. Subinterface-Based Network Design

|Feature|SVI-Based Design|Subinterface-Based Design|
|---|---|---|
|Layer 3 Location|Virtual interfaces tied to VLANs|Physical interfaces or logical subinterfaces|
|IP Assignment|IPs assigned to VLANs|IPs assigned to physical/logical interfaces|
|Link Type|Predominantly Layer 2 trunks|Predominantly Layer 3 routed links|
|Routing Domain|Centralized at virtual interfaces|Distributed across physical interfaces|
|IP Address Usage|More efficient (fewer point-to-point links)|Less efficient (requires /30 or /31 for each link)|
|Configuration Complexity|Lower (trunk once, create multiple SVIs)|Higher (configure each subinterface separately)|

## Key Advantages of Our SVI-Based Design

1. **Simplified VLAN Management**:
    - VLANs can be extended across the network using consistent Layer 2 trunks
    - New VLANs can be added without reconfiguring physical interfaces
2. **Efficient IP Address Utilization**:
    - Eliminates need for multiple /30 or /31 subnets for point-to-point links
    - Transit VLAN 150 creates a shared Layer 3 domain for multiple devices
3. **Streamlined High Availability Implementation**:
    - HSRP/VRRP works more naturally with SVIs than with routed interfaces
    - Allows for clean failover between redundant paths
4. **Enhanced Operational Simplicity**:
    - Consistent configuration model across the network
    - Easier troubleshooting with clearly defined Layer 2 and Layer 3 boundaries
5. **Improved Firewall Integration**:
    - Security devices often perform better with Layer 2 connections
    - Simplified high availability configurations for security devices
    - More flexible security zone implementation

## How Our Design Works: Layer 3 in Action

In our Sea Ice Creamery network:

- **Physical Links**: Primarily Layer 2 trunks carrying multiple VLANs
- **Layer 3 Routing**: Occurs on SVIs associated with specific VLANs
- **Transit VLAN 150**: Serves as the Layer 3 communication path between core switches and firewalls
- **Default Gateways**: Implemented as HSRP virtual IPs on core switches for campus VLANs
- **Security Boundaries**: Finance traffic is isolated through dedicated firewalls and VLANs

### Key VLAN and SVI Configurations


```
Core Switch SIC-MDF1-CORE-01:
interface VLAN10
  description DATA_USERS_SVI
  ip address 10.0.10.2 255.255.254.0
  standby 10 ip 10.0.10.1
  standby 10 priority 110
  standby 10 preempt delay minimum 180

interface VLAN150
  description FSP_TRANSIT_SVI
  ip address 10.0.150.2 255.255.255.248
```

## Traffic Flow Examples

### Example 1: User in IDF4 Getting an IP from DHCP

When a user connects to the network in IDF4 area:

1. **Client broadcasts DHCP DISCOVER**:
    - Message enters access switch SIC-IDF4-ACC-01 on VLAN 10
    - Switch forwards broadcast up to core via trunk (Port-channel15)
2. **Core switch processes the broadcast**:
    - SIC-MDF1-CORE-01 receives broadcast on VLAN 10
    - DHCP relay agent (configured on VLAN 10 SVI) forwards request to DHCP VIP (10.0.200.101)
3. **DHCP server processes request**:
    - Request arrives at active DHCP server via VLAN 200
    - Server generates DHCP OFFER with IP, subnet, and default gateway (10.0.10.1)
4. **Response returns to client**:
    - DHCP response travels from server through core switch
    - Core forwards down to access switch via trunk
    - Client receives IP configuration with HSRP virtual IP as gateway
5. **Client uses network**:
    - All outbound traffic is sent to default gateway (10.0.10.1)
    - HSRP ensures active core switch handles the traffic
    - If primary core fails, secondary takes over the HSRP address

### Example 2: Finance Department Traffic Flow

When a Finance user needs to access an internet resource:

1. **Finance client initiates connection**:
    - Client sends packet to its default gateway (10.0.100.1)
    - This address is hosted on Finance Firewall (not on core switches)
    - Traffic enters IDF2 access switch on VLAN 100
2. **Traffic reaches Finance Firewall**:
    - Packet travels to active Finance Firewall through trunk link
    - Firewall performs initial security inspection and NAT
    - Traffic is routed to FSP server via transit VLAN 150
3. **FSP server processes request**:
    - Request arrives at FSP server via VLAN 150
    - FSP performs content filtering and proxy services
    - Approved traffic is routed back to transit VLAN 150
4. **Traffic reaches Edge Firewall**:
    - From transit VLAN 150, traffic is routed to Edge Firewall
    - Edge Firewall performs final security checks and NAT to public IP
    - Traffic exits to internet via primary ISP
5. **Return traffic follows reverse path**:
    - Internet response comes back to Edge Firewall
    - Edge Firewall sends to FSP via transit VLAN 150
    - FSP sends to Finance Firewall via transit VLAN 150
    - Finance Firewall delivers to client via VLAN 100

The key point: **All routing occurs between VLAN interfaces (SVIs) rather than at physical interfaces**, creating a logical overlay of Layer 3 functionality on top of a primarily Layer 2 physical network.

## Defending This Design Approach

When presenting this design, emphasize these points:

1. **Industry-Standard Methodology**: "This SVI-based approach is widely used in enterprise environments for its scalability and operational efficiency."
2. **Optimized Resource Utilization**: "By centralizing Layer 3 functions to SVIs, we maximize IP address efficiency and simplify management."
3. **Enhanced Redundancy**: "The design enables seamless failover through HSRP and firewall HA without complex routing reconfigurations."
4. **Security Integration**: "Transit VLAN 150 creates a controlled security boundary that facilitates proper inspection of all Finance traffic."
5. **Future-Proofing**: "This approach makes it easier to add new VLANs or services without disruptive physical reconfigurations."

Remember that this design represents a conscious choice between equally valid approaches, made based on the specific requirements of Sea Ice Creamery's environment, including the security isolation needed for the Finance department and the high availability requirements.

## Technical Clarification for Common Questions

- **Q: Why don't the physical links have IP addresses?** **A:** They function as Layer 2 trunks, carrying VLANs (including transit VLAN 150) where IP addressing and routing occur.
- **Q: How does routing work between devices?** **A:** Routing occurs between the SVI interfaces, not on physical ports. Transit VLAN 150 creates a Layer 3 network shared across multiple devices.
- **Q: Why use HSRP instead of just static routes?** **A:** HSRP provides automatic failover for default gateways, ensuring continuous connectivity even if a core switch fails.
- **Q: How is the Finance department truly isolated?** **A:** Finance traffic uses separate VLANs (100, 110) with dedicated firewalls as gateways, forcing all traffic through security inspection before reaching other network zones.