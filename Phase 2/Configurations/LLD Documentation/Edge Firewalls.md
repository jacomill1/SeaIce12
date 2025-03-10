# Edge Firewalls Configuration
#tags: #network #firewall #edge #LLD #phase2

## SIC-MDF1-FW-01 (Primary Edge Firewall)
- **Management IP**: 10.0.250.11/26
- **Virtual IP (Shared)**: 10.0.150.4/29 (Floating IP for high availability)
- **Default Gateway**: N/A (This device serves as gateway)

### Interface Configuration
| Interface  | IP Address     | Subnet Mask     | Description      | Connected To               | VLAN                |
| ---------- | -------------- | --------------- | ---------------- | -------------------------- | ------------------- |
| eth1/1     | 152.162.97.182 | 255.255.255.252 | WAN_CENTURY_LINK | Century Link               | N/A                 |
| Aggregate1 | N/A            | N/A             | TO_CORE_SW       | Core Switches              | 10,20,30,40,150,250 |
| eth1/2     | 10.0.150.5     | 255.255.255.248 | TO_MDF1_CORE     | SIC-MDF1-CORE-01 (Te1/0/1) | 150                 |
| eth1/3     | 10.0.150.7     | 255.255.255.248 | TO_MDF2_CORE     | SIC-MDF2-CORE-01 (Te1/0/2) | 150                 |
| eth1/4     | N/A            | N/A             | HA1_LINK         | SIC-MDF2-FW-01 (eth1/4)    | N/A                 |
| eth1/5     | N/A            | N/A             | HA2_LINK         | SIC-MDF2-FW-01 (eth1/5)    | N/A                 |
| loopback0  | 10.0.250.101   | 255.255.255.255 | MGMT_LOOPBACK    | N/A                        | N/A                 |
| MGT        | 10.0.250.11    | 255.255.255.192 | OOB_MANAGEMENT   | Management Network         | 250                 |

### Aggregate Interface Configuration
- **Aggregate Group 1**: eth1/2-3 (To Core Switches)
  - **LACP Mode**: Active
  - **Description**: TO_CORE_SW
  - **Load Balancing**: Layer 3 hash
  - **Member Interfaces**:
    - eth1/2: Connected to SIC-MDF1-CORE-01 (Te1/0/1)
    - eth1/3: Connected to SIC-MDF2-CORE-01 (Te1/0/2)

### BGP Configuration
- **Local AS Number**: 6214 (Sea Ice Creamery's AS for Century Link peering)
- **Router ID**: 10.0.250.101
- **Neighbor Address**: 152.162.97.181
- **Neighbor AS Number**: 65510 (Century Link's AS)
- **Authentication**: Password (encrypted: 09617E2A5423051D05180D2F39)
- **Timers**: Keepalive 30, Hold time 90
- **BGP Policy**:
  - **Export Policy**: Advertise only Sea Ice Creamery public prefixes
  - **Import Policy**: Accept default route and specific routes from Century Link
  - **Weight**: 100 (Primary path for outbound traffic)

## SIC-MDF2-FW-01 (Secondary Edge Firewall)
- **Management IP**: 10.0.250.12/26
- **Virtual IP (Shared)**: 10.0.150.4/29 (Floating IP for high availability)
- **Default Gateway**: N/A (This device serves as gateway)

### Interface Configuration
| Interface | IP Address | Subnet Mask | Description | Connected To | VLAN |
|-----------|-----------|-------------|-------------|--------------|------|
| eth1/1 | 68.136.143.66 | 255.255.255.252 | WAN_COMCAST | Comcast | N/A |
| Aggregate1 | N/A | N/A | TO_CORE_SW | Core Switches | 10,20,30,40,150,250 |
| eth1/2 | 10.0.150.6 | 255.255.255.248 | TO_MDF2_CORE | SIC-MDF2-CORE-01 (Te1/0/1) | 150 |
| eth1/3 | 10.0.150.8 | 255.255.255.248 | TO_MDF1_CORE | SIC-MDF1-CORE-01 (Te1/0/2) | 150 |
| eth1/4 | N/A | N/A | HA1_LINK | SIC-MDF1-FW-01 (eth1/4) | N/A |
| eth1/5 | N/A | N/A | HA2_LINK | SIC-MDF1-FW-01 (eth1/5) | N/A |
| loopback0 | 10.0.250.102 | 255.255.255.255 | MGMT_LOOPBACK | N/A | N/A |
| MGT | 10.0.250.12 | 255.255.255.192 | OOB_MANAGEMENT | Management Network | 250 |

### Aggregate Interface Configuration
- **Aggregate Group 1**: eth1/2-3 (To Core Switches)
  - **LACP Mode**: Active
  - **Description**: TO_CORE_SW
  - **Load Balancing**: Layer 3 hash
  - **Member Interfaces**:
    - eth1/2: Connected to SIC-MDF2-CORE-01 (Te1/0/1)
    - eth1/3: Connected to SIC-MDF1-CORE-01 (Te1/0/2)

### BGP Configuration
- **Local AS Number**: 6211 (Sea Ice Creamery's AS for Comcast peering)
- **Router ID**: 10.0.250.102
- **Neighbor Address**: 68.136.143.65
- **Neighbor AS Number**: 65000 (Comcast's AS)
- **Authentication**: Password (encrypted: 0961612A542702010202013938)
- **Timers**: Keepalive 30, Hold time 90
- **BGP Policy**:
  - **Export Policy**: Advertise only Sea Ice Creamery public prefixes
  - **Import Policy**: Accept default route and specific routes from Comcast
  - **Weight**: 90 (Secondary path for outbound traffic)

### High Availability Configuration
- **Mode**: Active/Passive
- **Group ID**: 1
- **Virtual IPs**: 10.0.150.4/29 (Transit)
- **Pre-emption**: Enabled
- **Heartbeat Interfaces**: eth1/4 (HA1), eth1/5 (HA2)