# Core Switches Configuration
#tags: #network #core #switch #LLD #phase2 #cisco

## SIC-MDF1-CORE-01
- **Management IP**: 10.0.250.2/26
- **Default Gateway**: 10.0.250.1 (Virtual IP)

### Interface Configuration
| Interface | IP Address/VLAN | Description | Connected To | Mode | VLANs |
|-----------|----------------|-------------|--------------|------|-------|
| VLAN10 | 10.0.10.2/23 | DATA_USERS_SVI | N/A | N/A | N/A |
| VLAN20 | 10.0.20.2/23 | VOICE_SVI | N/A | N/A | N/A |
| VLAN30 | 10.0.30.2/22 | WIRELESS_SVI | N/A | N/A | N/A |
| VLAN40 | 10.0.40.2/22 | GUEST_WIRELESS_SVI | N/A | N/A | N/A |
| VLAN100 | 10.0.100.2/25 | FINANCE_DATA_SVI | N/A | N/A | N/A |
| VLAN110 | 10.0.110.2/25 | FINANCE_VOICE_SVI | N/A | N/A | N/A |
| VLAN150 | 10.0.150.2/29 | FSP_TRANSIT_SVI | N/A | N/A | N/A |
| VLAN200 | 10.0.200.2/26 | NETWORK_SERVICES_SVI | N/A | N/A | N/A |
| VLAN250 | 10.0.250.2/26 | MANAGEMENT_SVI | N/A | N/A | N/A |
| VLAN254 | 10.0.254.1/30 | MDF_ROUTING_SVI | N/A | N/A | N/A |
| Te1/0/1 | N/A | TO_MDF1_FW | SIC-MDF1-FW-01 (eth1/2) | Trunk | 10,20,30,40,150,250 |
| Te1/0/2 | N/A | TO_MDF2_FW | SIC-MDF2-FW-01 (eth1/3) | Trunk | 10,20,30,40,150,250 |
| Te1/0/3 | N/A | TO_MDF1_FNFW | SIC-MDF1-FNFW-01 (eth1/2) | Trunk | 150,250 |
| Te1/0/4 | N/A | TO_MDF2_FNFW | SIC-MDF2-FNFW-01 (eth1/3) | Trunk | 150,250 |
| Port-channel5 | N/A | TO_MDF2_CORE | SIC-MDF2-CORE-01 | Trunk | All |
| Te1/0/5-8 | N/A | TO_MDF2_CORE | SIC-MDF2-CORE-01 (Te1/0/5-8) | Trunk | All |
| Port-channel9 | N/A | TO_IDF1_ACC | SIC-IDF1-ACC-01 | Trunk | 10,20,30,40,250 |
| Te1/0/9-10 | N/A | TO_IDF1_ACC | SIC-IDF1-ACC-01 (Te1/0/1-2) | Trunk | 10,20,30,40,250 |
| Port-channel13 | N/A | TO_IDF3_ACC | SIC-IDF3-ACC-01 | Trunk | 10,20,30,40,250 |
| Te1/0/13-14 | N/A | TO_IDF3_ACC | SIC-IDF3-ACC-01 (Te1/0/1-2) | Trunk | 10,20,30,40,250 |
| Port-channel15 | N/A | TO_IDF4_ACC | SIC-IDF4-ACC-01 | Trunk | 10,20,30,40,250 |
| Te1/0/15-16 | N/A | TO_IDF4_ACC | SIC-IDF4-ACC-01 (Te1/0/1-2) | Trunk | 10,20,30,40,250 |
| Port-channel21 | N/A | TO_DHCP_SERVER | SIC-MDF1-DHCP | Access | 200 |
| Gi1/0/1-2 | N/A | TO_DHCP_SERVER | SIC-MDF1-DHCP (eth0-1) | Access | 200 |
| Port-channel22 | N/A | TO_DNS_SERVER | SIC-MDF1-DNS | Access | 200 |
| Gi1/0/3-4 | N/A | TO_DNS_SERVER | SIC-MDF1-DNS (eth0-1) | Access | 200 |
| Port-channel23 | N/A | TO_VG_SERVER | SIC-MDF1-VG | Access | 200 |
| Gi1/0/5-6 | N/A | TO_VG_SERVER | SIC-MDF1-VG (g0/0-1) | Access | 200 |
| Port-channel24 | N/A | TO_WLC | SIC-MDF1-WLC-01 | Trunk | 30,40,250 |
| Gi1/0/7-8 | N/A | TO_WLC | SIC-MDF1-WLC-01 (port1-2) | Trunk | 30,40,250 |
| Gi1/0/24 | N/A | TO_MGMT_NET | Management Network | Access | 250 |
| loopback0 | 10.0.250.50 | MGMT_LOOPBACK | N/A | N/A | N/A |

### Port Channel Configuration
- **Port-Channel 5**: Te1/0/5-8 (To MDF2 Core)
  - **LACP Mode**: Active
  - **Description**: TO_MDF2_CORE
  - **Load Balancing**: src-dst-ip
  - **Member Interfaces**:
    - Te1/0/5-8: To SIC-MDF2-CORE-01

- **Port-Channel 9**: Te1/0/9-10 (To IDF1)
  - **LACP Mode**: Active
  - **Description**: TO_IDF1_ACC
  - **Load Balancing**: src-dst-mac
  - **Member Interfaces**:
    - Te1/0/9-10: To SIC-IDF1-ACC-01

- **Port-Channel 13**: Te1/0/13-14 (To IDF3)
  - **LACP Mode**: Active
  - **Description**: TO_IDF3_ACC
  - **Load Balancing**: src-dst-mac
  - **Member Interfaces**:
    - Te1/0/13-14: To SIC-IDF3-ACC-01

- **Port-Channel 15**: Te1/0/15-16 (To IDF4)
  - **LACP Mode**: Active
  - **Description**: TO_IDF4_ACC
  - **Load Balancing**: src-dst-mac
  - **Member Interfaces**:
    - Te1/0/15-16: To SIC-IDF4-ACC-01
  
- **Port-Channel 21**: Gi1/0/1-2 (To DHCP Server)
  - **LACP Mode**: Active
  - **Description**: TO_DHCP_SERVER
  - **Load Balancing**: src-dst-mac
  - **Member Interfaces**:
    - Gi1/0/1-2: To SIC-MDF1-DHCP

- **Port-Channel 22**: Gi1/0/3-4 (To DNS Server)
  - **LACP Mode**: Active
  - **Description**: TO_DNS_SERVER
  - **Load Balancing**: src-dst-mac
  - **Member Interfaces**:
    - Gi1/0/3-4: To SIC-MDF1-DNS

- **Port-Channel 23**: Gi1/0/5-6 (To Voice Gateway)
  - **LACP Mode**: Active
  - **Description**: TO_VG_SERVER
  - **Load Balancing**: src-dst-mac
  - **Member Interfaces**:
    - Gi1/0/5-6: To SIC-MDF1-VG

- **Port-Channel 24**: Gi1/0/7-8 (To WLC)
  - **LACP Mode**: Active
  - **Description**: TO_WLC
  - **Load Balancing**: src-dst-ip
  - **Member Interfaces**:
    - Gi1/0/7-8: To SIC-MDF1-WLC-01

### HSRP Configuration
- **Primary for VLANs**: 10, 30, 100, 200
  - **Group Numbers**: 10, 30, 100, 200
  - **Priority**: 110
  - **Preempt**: Enabled with delay minimum 180 seconds
  - **Virtual IPs**: 10.0.10.1, 10.0.30.1, 10.0.100.1, 10.0.200.1
  - **Timers**: Hello 3, Hold 10
  - **Authentication**: MD5

- **Secondary for VLANs**: 20, 40, 110, 250
  - **Group Numbers**: 20, 40, 110, 250
  - **Priority**: 90
  - **Preempt**: Disabled
  - **Virtual IPs**: 10.0.20.1, 10.0.40.1, 10.0.110.1, 10.0.250.1

## SIC-MDF2-CORE-01
- **Management IP**: 10.0.250.3/26
- **Default Gateway**: 10.0.250.1 (Virtual IP)

### Interface Configuration
| Interface | IP Address/VLAN | Description | Connected To | Mode | VLANs |
|-----------|----------------|-------------|--------------|------|-------|
| VLAN10 | 10.0.10.3/23 | DATA_USERS_SVI | N/A | N/A | N/A |
| VLAN20 | 10.0.20.3/23 | VOICE_SVI | N/A | N/A | N/A |
| VLAN30 | 10.0.30.3/22 | WIRELESS_SVI | N/A | N/A | N/A |
| VLAN40 | 10.0.40.3/22 | GUEST_WIRELESS_SVI | N/A | N/A | N/A |
| VLAN100 | 10.0.100.3/25 | FINANCE_DATA_SVI | N/A | N/A | N/A |
| VLAN110 | 10.0.110.3/25 | FINANCE_VOICE_SVI | N/A | N/A | N/A |
| VLAN150 | 10.0.150.3/29 | FSP_TRANSIT_SVI | N/A | N/A | N/A |
| VLAN200 | 10.0.200.3/26 | NETWORK_SERVICES_SVI | N/A | N/A | N/A |
| VLAN250 | 10.0.250.3/26 | MANAGEMENT_SVI | N/A | N/A | N/A |
| VLAN254 | 10.0.254.2/30 | MDF_ROUTING_SVI | N/A | N/A | N/A |
| Te1/0/1 | N/A | TO_MDF2_FW | SIC-MDF2-FW-01 (eth1/2) | Trunk | 10,20,30,40,150,250 |
| Te1/0/2 | N/A | TO_MDF1_FW | SIC-MDF1-FW-01 (eth1/3) | Trunk | 10,20,30,40,150,250 |
| Te1/0/3 | N/A | TO_MDF2_FNFW | SIC-MDF2-FNFW-01 (eth1/2) | Trunk | 150,250 |
| Te1/0/4 | N/A | TO_MDF1_FNFW | SIC-MDF1-FNFW-01 (eth1/3) | Trunk | 150,250 |
| Port-channel5 | N/A | TO_MDF1_CORE | SIC-MDF1-CORE-01 | Trunk | All |
| Te1/0/5-8 | N/A | TO_MDF1_CORE | SIC-MDF1-CORE-01 (Te1/0/5-8) | Trunk | All |
| Port-channel9 | N/A | TO_IDF1_ACC | SIC-IDF1-ACC-01 | Trunk | 10,20,30,40,250 |
| Te1/0/9-10 | N/A | TO_IDF1_ACC | SIC-IDF1-ACC-01 (Te1/0/3-4) | Trunk | 10,20,30,40,250 |
| Port-channel13 | N/A | TO_IDF3_ACC | SIC-IDF3-ACC-01 | Trunk | 10,20,30,40,250 |
| Te1/0/13-14 | N/A | TO_IDF3_ACC | SIC-IDF3-ACC-01 (Te1/0/3-4) | Trunk | 10,20,30,40,250 |
| Port-channel15 | N/A | TO_IDF4_ACC | SIC-IDF4-ACC-01 | Trunk | 10,20,30,40,250 |
| Te1/0/15-16 | N/A | TO_IDF4_ACC | SIC-IDF4-ACC-01 (Te1/0/3-4) | Trunk | 10,20,30,40,250 |
| Port-channel21 | N/A | TO_DHCP_SERVER | SIC-MDF2-DHCP | Access | 200 |
| Gi1/0/1-2 | N/A | TO_DHCP_SERVER | SIC-MDF2-DHCP (eth0-1) | Access | 200 |
| Port-channel22 | N/A | TO_DNS_SERVER | SIC-MDF2-DNS | Access | 200 |
| Gi1/0/3-4 | N/A | TO_DNS_SERVER | SIC-MDF2-DNS (eth0-1) | Access | 200 |
| Port-channel23 | N/A | TO_VG_SERVER | SIC-MDF2-VG | Access | 200 |
| Gi1/0/5-6 | N/A | TO_VG_SERVER | SIC-MDF2-VG (g0/0-1) | Access | 200 |
| Port-channel24 | N/A | TO_WLC | SIC-MDF2-WLC-01 | Trunk | 30,40,250 |
| Gi1/0/7-8 | N/A | TO_WLC | SIC-MDF2-WLC-01 (port1-2) | Trunk | 30,40,250 |
| Gi1/0/24 | N/A | TO_MGMT_NET | Management Network | Access | 250 |
| loopback0 | 10.0.250.51 | MGMT_LOOPBACK | N/A | N/A | N/A |

### Port Channel Configuration
- **Port-Channel 5**: Te1/0/5-8 (To MDF1 Core)
  - **LACP Mode**: Active
  - **Description**: TO_MDF1_CORE
  - **Load Balancing**: src-dst-ip
  - **Member Interfaces**:
    - Te1/0/5-8: To SIC-MDF1-CORE-01

- **Port-Channel 9**: Te1/0/9-10 (To IDF1)
  - **LACP Mode**: Active
  - **Description**: TO_IDF1_ACC
  - **Load Balancing**: src-dst-mac
  - **Member Interfaces**:
    - Te1/0/9-10: To SIC-IDF1-ACC-01

- **Port-Channel 13**: Te1/0/13-14 (To IDF3)
  - **LACP Mode**: Active
  - **Description**: TO_IDF3_ACC
  - **Load Balancing**: src-dst-mac
  - **Member Interfaces**:
    - Te1/0/13-14: To SIC-IDF3-ACC-01

- **Port-Channel 15**: Te1/0/15-16 (To IDF4)
  - **LACP Mode**: Active
  - **Description**: TO_IDF4_ACC
  - **Load Balancing**: src-dst-mac
  - **Member Interfaces**:
    - Te1/0/15-16: To SIC-IDF4-ACC-01
  
- **Port-Channel 21**: Gi1/0/1-2 (To DHCP Server)
  - **LACP Mode**: Active
  - **Description**: TO_DHCP_SERVER
  - **Load Balancing**: src-dst-mac
  - **Member Interfaces**:
    - Gi1/0/1-2: To SIC-MDF2-DHCP

- **Port-Channel 22**: Gi1/0/3-4 (To DNS Server)
  - **LACP Mode**: Active
  - **Description**: TO_DNS_SERVER
  - **Load Balancing**: src-dst-mac
  - **Member Interfaces**:
    - Gi1/0/3-4: To SIC-MDF2-DNS

- **Port-Channel 23**: Gi1/0/5-6 (To Voice Gateway)
  - **LACP Mode**: Active
  - **Description**: TO_VG_SERVER
  - **Load Balancing**: src-dst-mac
  - **Member Interfaces**:
    - Gi1/0/5-6: To SIC-MDF2-VG

- **Port-Channel 24**: Gi1/0/7-8 (To WLC)
  - **LACP Mode**: Active
  - **Description**: TO_WLC
  - **Load Balancing**: src-dst-ip
  - **Member Interfaces**:
    - Gi1/0/7-8: To SIC-MDF2-WLC-01

### HSRP Configuration
- **Primary for VLANs**: 20, 40, 110, 250
  - **Group Numbers**: 20, 40, 110, 250
  - **Priority**: 110
  - **Preempt**: Enabled with delay minimum 180 seconds
  - **Virtual IPs**: 10.0.20.1, 10.0.40.1, 10.0.110.1, 10.0.250.1
  - **Timers**: Hello 3, Hold 10
  - **Authentication**: MD5

- **Secondary for VLANs**: 10, 30, 100, 200
  - **Group Numbers**: 10, 30, 100, 200
  - **Priority**: 90
  - **Preempt**: Disabled
  - **Virtual IPs**: 10.0.10.1, 10.0.30.1, 10.0.100.1, 10.0.200.1