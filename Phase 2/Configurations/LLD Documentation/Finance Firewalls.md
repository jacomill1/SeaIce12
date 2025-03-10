# Finance Firewalls Configuration
#tags: #network #firewall #finance #security #LLD #phase2

## SIC-MDF1-FNFW-01 (Primary Finance Firewall)
- **Management IP**: 10.0.250.21/26
- **Virtual IPs**: 
  - 10.0.100.1/25 (Finance Data)
  - 10.0.110.1/25 (Finance Voice)
  - 10.0.150.9/29 (FSP Transit - Floating IP for high availability)
- **Default Gateway**: N/A (This device serves as gateway)

### Interface Configuration
| Interface | IP Address | Subnet Mask | Description | Connected To | VLAN |
|-----------|-----------|-------------|-------------|--------------|------|
| eth1/1.100 | 10.0.100.5 | 255.255.255.128 | FINANCE_DATA | SIC-IDF2-ACC-01 | 100 |
| eth1/1.110 | 10.0.110.5 | 255.255.255.128 | FINANCE_VOICE | SIC-IDF2-ACC-01 | 110 |
| Aggregate1 | N/A | N/A | TO_CORE_SW | Core Switches | 150,250 |
| eth1/2 | 10.0.150.10 | 255.255.255.248 | TO_MDF1_CORE | SIC-MDF1-CORE-01 (Te1/0/3) | 150 |
| eth1/3 | 10.0.150.12 | 255.255.255.248 | TO_MDF2_CORE | SIC-MDF2-CORE-01 (Te1/0/4) | 150 |
| eth1/4 | N/A | N/A | HA1_LINK | SIC-MDF2-FNFW-01 (eth1/4) | N/A |
| eth1/5 | N/A | N/A | HA2_LINK | SIC-MDF2-FNFW-01 (eth1/5) | N/A |
| loopback0 | 10.0.250.103 | 255.255.255.255 | MGMT_LOOPBACK | N/A | N/A |
| MGT | 10.0.250.21 | 255.255.255.192 | OOB_MANAGEMENT | Management Network | 250 |

### Aggregate Interface Configuration
- **Aggregate Group 1**: eth1/2-3 (To Core Switches)
  - **LACP Mode**: Active
  - **Description**: TO_CORE_SW
  - **Load Balancing**: Layer 3 hash
  - **Member Interfaces**:
    - eth1/2: Connected to SIC-MDF1-CORE-01 (Te1/0/3)
    - eth1/3: Connected to SIC-MDF2-CORE-01 (Te1/0/4)

### OSPF Configuration
- **Process ID**: 1
- **Router ID**: 10.0.250.103
- **Area**: 0
- **Networks**:
  - 10.0.100.0/25 area 0 (Finance Data)
  - 10.0.110.0/25 area 0 (Finance Voice)
  - 10.0.150.8/29 area 0 (FSP Transit)
  - 10.0.250.0/26 area 0 (Management)

## SIC-MDF2-FNFW-01 (Secondary Finance Firewall)
- **Management IP**: 10.0.250.22/26
- **Virtual IPs**: 
  - 10.0.100.1/25 (Finance Data)
  - 10.0.110.1/25 (Finance Voice)
  - 10.0.150.9/29 (FSP Transit - Floating IP for high availability)
- **Default Gateway**: N/A (This device serves as gateway)

### Interface Configuration
| Interface | IP Address | Subnet Mask | Description | Connected To | VLAN |
|-----------|-----------|-------------|-------------|--------------|------|
| eth1/1.100 | 10.0.100.6 | 255.255.255.128 | FINANCE_DATA | SIC-IDF2-ACC-01 | 100 |
| eth1/1.110 | 10.0.110.6 | 255.255.255.128 | FINANCE_VOICE | SIC-IDF2-ACC-01 | 110 |
| Aggregate1 | N/A | N/A | TO_CORE_SW | Core Switches | 150,250 |
| eth1/2 | 10.0.150.11 | 255.255.255.248 | TO_MDF2_CORE | SIC-MDF2-CORE-01 (Te1/0/3) | 150 |
| eth1/3 | 10.0.150.13 | 255.255.255.248 | TO_MDF1_CORE | SIC-MDF1-CORE-01 (Te1/0/4) | 150 |
| eth1/4 | N/A | N/A | HA1_LINK | SIC-MDF1-FNFW-01 (eth1/4) | N/A |
| eth1/5 | N/A | N/A | HA2_LINK | SIC-MDF1-FNFW-01 (eth1/5) | N/A |
| loopback0 | 10.0.250.104 | 255.255.255.255 | MGMT_LOOPBACK | N/A | N/A |
| MGT | 10.0.250.22 | 255.255.255.192 | OOB_MANAGEMENT | Management Network | 250 |

### Aggregate Interface Configuration
- **Aggregate Group 1**: eth1/2-3 (To Core Switches)
  - **LACP Mode**: Active
  - **Description**: TO_CORE_SW
  - **Load Balancing**: Layer 3 hash
  - **Member Interfaces**:
    - eth1/2: Connected to SIC-MDF2-CORE-01 (Te1/0/3)
    - eth1/3: Connected to SIC-MDF1-CORE-01 (Te1/0/4)

### High Availability Configuration
- **Mode**: Active/Passive
- **Group ID**: 2
- **Virtual IPs**: 
  - 10.0.100.1/25 (Finance Data VLAN)
  - 10.0.110.1/25 (Finance Voice VLAN)
  - 10.0.150.9/29 (Transit VLAN)
- **Pre-emption**: Enabled
- **Heartbeat Interfaces**: eth1/4 (HA1), eth1/5 (HA2)