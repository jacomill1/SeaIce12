# Edge Firewall NAT Configuration
#tags: #network #firewall #NAT #PAT #LLD #phase2

## NAT Overview
Network Address Translation (NAT) is implemented on the edge firewalls to allow communication between the private 10.0.0.0/8 network and the internet. Port Address Translation (PAT) is used to efficiently utilize public IP address space by mapping multiple internal addresses to a single public IP using different ports.

## Source NAT (Outbound) Policies

### Primary Edge Firewall (SIC-MDF1-FW-01)
- **Public IP Address**: 152.162.97.182/30 (Century Link WAN)
- **NAT Type**: Dynamic IP and Port (PAT)
- **Overload Factor**: Enabled

| Rule Name | Source Zone | Source Address | Destination Zone | Destination Address | Translation Type | Translated Source | Description |
|-----------|-------------|----------------|------------------|---------------------|------------------|-------------------|-------------|
| OUTBOUND_NAT_USERS | Internal | 10.0.10.0/23 | External | Any | Dynamic IP and Port | 152.162.97.182 | User Data VLAN outbound NAT |
| OUTBOUND_NAT_VOICE | Internal | 10.0.20.0/23 | External | Any | Dynamic IP and Port | 152.162.97.182 | Voice VLAN outbound NAT |
| OUTBOUND_NAT_WIRELESS | Internal | 10.0.30.0/22 | External | Any | Dynamic IP and Port | 152.162.97.182 | Wireless VLAN outbound NAT |
| OUTBOUND_NAT_GUEST | Internal | 10.0.40.0/22 | External | Any | Dynamic IP and Port | 152.162.97.182 | Guest Wireless VLAN outbound NAT |
| OUTBOUND_NAT_FINANCE | Internal | 10.0.100.0/25, 10.0.110.0/25 | External | Any | Dynamic IP and Port | 152.162.97.182 | Finance VLANs outbound NAT |
| OUTBOUND_NAT_SERVICES | Internal | 10.0.200.0/26 | External | Any | Dynamic IP and Port | 152.162.97.182 | Network Services VLAN outbound NAT |

### Secondary Edge Firewall (SIC-MDF2-FW-01)
- **Public IP Address**: 68.136.143.66/30 (Comcast WAN)
- **NAT Type**: Dynamic IP and Port (PAT)
- **Overload Factor**: Enabled

| Rule Name | Source Zone | Source Address | Destination Zone | Destination Address | Translation Type | Translated Source | Description |
|-----------|-------------|----------------|------------------|---------------------|------------------|-------------------|-------------|
| OUTBOUND_NAT_USERS | Internal | 10.0.10.0/23 | External | Any | Dynamic IP and Port | 68.136.143.66 | User Data VLAN outbound NAT |
| OUTBOUND_NAT_VOICE | Internal | 10.0.20.0/23 | External | Any | Dynamic IP and Port | 68.136.143.66 | Voice VLAN outbound NAT |
| OUTBOUND_NAT_WIRELESS | Internal | 10.0.30.0/22 | External | Any | Dynamic IP and Port | 68.136.143.66 | Wireless VLAN outbound NAT |
| OUTBOUND_NAT_GUEST | Internal | 10.0.40.0/22 | External | Any | Dynamic IP and Port | 68.136.143.66 | Guest Wireless VLAN outbound NAT |
| OUTBOUND_NAT_FINANCE | Internal | 10.0.100.0/25, 10.0.110.0/25 | External | Any | Dynamic IP and Port | 68.136.143.66 | Finance VLANs outbound NAT |
| OUTBOUND_NAT_SERVICES | Internal | 10.0.200.0/26 | External | Any | Dynamic IP and Port | 68.136.143.66 | Network Services VLAN outbound NAT |

## Destination NAT (Inbound Services)

The following services are exposed to the internet through destination NAT:

| Rule Name | External Interface | Public IP | Public Port | Translated Destination | Private Port | Protocol | Description |
|-----------|-------------------|-----------|------------|------------------------|--------------|----------|-------------|
| DMZ_WEB | eth1/1 | 152.162.97.182 | 443 | 10.0.200.21 | 443 | TCP | Corporate Website (Primary ISP) |
| DMZ_WEB_BACKUP | eth1/1 | 68.136.143.66 | 443 | 10.0.200.21 | 443 | TCP | Corporate Website (Secondary ISP) |
| VPN_ACCESS | eth1/1 | 152.162.97.182 | 4500, 500 | 10.0.200.31 | 4500, 500 | UDP | VPN Access (Primary ISP) |
| VPN_ACCESS_BACKUP | eth1/1 | 68.136.143.66 | 4500, 500 | 10.0.200.31 | 4500, 500 | UDP | VPN Access (Secondary ISP) |

## NAT Exclusions (No NAT)

The following traffic types bypass NAT:

| Rule Name | Source Zone | Source Address | Destination Zone | Destination Address | Action | Description |
|-----------|-------------|----------------|------------------|---------------------|--------|-------------|
| NO_NAT_VPN | Internal | 10.0.0.0/8 | VPN | 172.16.0.0/12 | No NAT | VPN Traffic to Branch Offices |
| NO_NAT_INTERNAL | Internal | 10.0.0.0/8 | Internal | 10.0.0.0/8 | No NAT | Internal to Internal Traffic |

## PAT Configuration

Port Address Translation is configured with the following parameters:

- **Translation Slot Allocation**: 1:1
- **Port Range Start**: 1024
- **Port Range End**: 65535
- **Round Robin Allocation**: Enabled
- **Source Port Preservation**: Enabled (Best Effort)
- **Allocation Algorithm**: Randomized
- **Session Timeout (TCP)**: 3600 seconds
- **Session Timeout (UDP)**: 30 seconds
- **Maximum Sessions Per Host**: 250,000

## NAT Failover Configuration

In the event of ISP failure, the following NAT failover mechanisms are in place:

| Primary ISP | Secondary ISP | Failover Trigger | Recovery Action | Timeout |
|-------------|---------------|------------------|-----------------|---------|
| Century Link (152.162.97.182) | Comcast (68.136.143.66) | 3 consecutive ping failures | Automatic failover to secondary | 15 seconds |
| Both ISPs active | N/A | N/A | Active-Active load balancing | N/A |

### NAT Policy Synchronization
- NAT states are synchronized between the active and passive firewalls
- Session persistence is maintained during failover
- Maximum synchronization delay: 3 seconds