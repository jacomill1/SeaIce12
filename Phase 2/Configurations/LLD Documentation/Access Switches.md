# Access Switches Configuration (CORRECTED)
#tags: #network #access #switch #LLD #phase2 #cisco

## IDF1 Access Switch (SIC-IDF1-ACC-01)
- **Management IP**: 10.0.250.31/26
- **Default Gateway**: 10.0.250.1 (Virtual IP)

### Interface Configuration
| Interface     | Description  | Mode   | VLAN            | Connected To                  |
| ------------- | ------------ | ------ | --------------- | ----------------------------- |
| Port-channel1 | TO_MDF1_CORE | Trunk  | 10,20,30,40,250 | SIC-MDF1-CORE-01              |
| Te1/0/1-2     | TO_MDF1_CORE | Trunk  | 10,20,30,40,250 | SIC-MDF1-CORE-01 (Te1/0/9-10) |
| Port-channel2 | TO_MDF2_CORE | Trunk  | 10,20,30,40,250 | SIC-MDF2-CORE-01              |
| Te1/0/3-4     | TO_MDF2_CORE | Trunk  | 10,20,30,40,250 | SIC-MDF2-CORE-01 (Te1/0/9-10) |
| Gi1/0/1-19    | USER_DATA    | Access | 10              | User Workstations             |
| Gi1/0/20-30   | USER_VOICE   | Access | 20              | VoIP Phones                   |
| Gi1/0/31-32   | AP_PORTS     | Trunk  | 30,40           | Wireless Access Points        |
| Gi1/0/48      | MGMT_PORT    | Access | 250             | Management Network            |

### Port Channel Configuration
- **Port-Channel 1**: Te1/0/1-2 (To MDF1)
  - **LACP Mode**: Active
  - **Description**: TO_MDF1_CORE
  - **Member Interfaces**:
    - Te1/0/1: To SIC-MDF1-CORE-01 (Te1/0/9)
    - Te1/0/2: To SIC-MDF1-CORE-01 (Te1/0/10)

- **Port-Channel 2**: Te1/0/3-4 (To MDF2)
  - **LACP Mode**: Active
  - **Description**: TO_MDF2_CORE
  - **Member Interfaces**:
    - Te1/0/3: To SIC-MDF2-CORE-01 (Te1/0/9)
    - Te1/0/4: To SIC-MDF2-CORE-01 (Te1/0/10)

### Spanning Tree Configuration
- **Mode**: Rapid-PVST+
- **Root Guard**: Enabled on access ports
- **BPDU Guard**: Enabled on access ports
- **Portfast**: Enabled on access ports

## IDF2 Access Switch (SIC-IDF2-ACC-01) - Finance Department
- **Management IP**: 10.0.250.32/26
- **Default Gateway**: 10.0.250.1 (Virtual IP)

### Interface Configuration
| Interface     | Description   | Mode   | VLAN        | Connected To              |
| ------------- | ------------- | ------ | ----------- | ------------------------- |
| Port-channel1 | TO_MDF1_FNFW  | Trunk  | 100,110,250 | SIC-MDF1-FNFW-01          |
| Te1/0/1-2     | TO_MDF1_FNFW  | Trunk  | 100,110,250 | SIC-MDF1-FNFW-01 (eth1/1) |
| Port-channel2 | TO_MDF2_FNFW  | Trunk  | 100,110,250 | SIC-MDF2-FNFW-01          |
| Te1/0/3-4     | TO_MDF2_FNFW  | Trunk  | 100,110,250 | SIC-MDF2-FNFW-01 (eth1/1) |
| Gi1/0/1-19    | FINANCE_DATA  | Access | 100         | Finance Workstations      |
| Gi1/0/20-30   | FINANCE_VOICE | Access | 110         | Finance VoIP Phones       |
| Gi1/0/48      | MGMT_PORT     | Access | 250         | Management Network        |

### Port Channel Configuration
- **Port-Channel 1**: Te1/0/1-2 (To MDF1-FNFW)
  - **LACP Mode**: Active
  - **Description**: TO_MDF1_FNFW
  - **Member Interfaces**:
    - Te1/0/1: To SIC-MDF1-FNFW-01 (eth1/1.1)
    - Te1/0/2: To SIC-MDF1-FNFW-01 (eth1/1.2)

- **Port-Channel 2**: Te1/0/3-4 (To MDF2-FNFW)
  - **LACP Mode**: Active
  - **Description**: TO_MDF2_FNFW
  - **Member Interfaces**:
    - Te1/0/3: To SIC-MDF2-FNFW-01 (eth1/1.1)
    - Te1/0/4: To SIC-MDF2-FNFW-01 (eth1/1.2)

### Spanning Tree Configuration
- **Mode**: Rapid-PVST+
- **Root Guard**: Enabled on access ports
- **BPDU Guard**: Enabled on access ports
- **Portfast**: Enabled on access ports

## IDF3 Access Switch (SIC-IDF3-ACC-01)
- **Management IP**: 10.0.250.33/26
- **Default Gateway**: 10.0.250.1 (Virtual IP)

### Interface Configuration
| Interface     | Description  | Mode   | VLAN            | Connected To                   |
| ------------- | ------------ | ------ | --------------- | ------------------------------ |
| Port-channel1 | TO_MDF1_CORE | Trunk  | 10,20,30,40,250 | SIC-MDF1-CORE-01               |
| Te1/0/1-2     | TO_MDF1_CORE | Trunk  | 10,20,30,40,250 | SIC-MDF1-CORE-01 (Te1/0/13-14) |
| Port-channel2 | TO_MDF2_CORE | Trunk  | 10,20,30,40,250 | SIC-MDF2-CORE-01               |
| Te1/0/3-4     | TO_MDF2_CORE | Trunk  | 10,20,30,40,250 | SIC-MDF2-CORE-01 (Te1/0/13-14) |
| Gi1/0/1-19    | USER_DATA    | Access | 10              | User Workstations              |
| Gi1/0/20-30   | USER_VOICE   | Access | 20              | VoIP Phones                    |
| Gi1/0/31-32   | AP_PORTS     | Trunk  | 30,40           | Wireless Access Points         |
| Gi1/0/48      | MGMT_PORT    | Access | 250             | Management Network             |

### Port Channel Configuration
- **Port-Channel 1**: Te1/0/1-2 (To MDF1)
  - **LACP Mode**: Active
  - **Description**: TO_MDF1_CORE
  - **Member Interfaces**:
    - Te1/0/1: To SIC-MDF1-CORE-01 (Te1/0/13)
    - Te1/0/2: To SIC-MDF1-CORE-01 (Te1/0/14)

- **Port-Channel 2**: Te1/0/3-4 (To MDF2)
  - **LACP Mode**: Active
  - **Description**: TO_MDF2_CORE
  - **Member Interfaces**:
    - Te1/0/3: To SIC-MDF2-CORE-01 (Te1/0/13)
    - Te1/0/4: To SIC-MDF2-CORE-01 (Te1/0/14)

### Spanning Tree Configuration
- **Mode**: Rapid-PVST+
- **Root Guard**: Enabled on access ports
- **BPDU Guard**: Enabled on access ports
- **Portfast**: Enabled on access ports

## IDF4 Access Switch (SIC-IDF4-ACC-01)
- **Management IP**: 10.0.250.34/26
- **Default Gateway**: 10.0.250.1 (Virtual IP)

### Interface Configuration
| Interface     | Description  | Mode   | VLAN            | Connected To                   |
| ------------- | ------------ | ------ | --------------- | ------------------------------ |
| Port-channel1 | TO_MDF1_CORE | Trunk  | 10,20,30,40,250 | SIC-MDF1-CORE-01               |
| Te1/0/1-2     | TO_MDF1_CORE | Trunk  | 10,20,30,40,250 | SIC-MDF1-CORE-01 (Te1/0/15-16) |
| Port-channel2 | TO_MDF2_CORE | Trunk  | 10,20,30,40,250 | SIC-MDF2-CORE-01               |
| Te1/0/3-4     | TO_MDF2_CORE | Trunk  | 10,20,30,40,250 | SIC-MDF2-CORE-01 (Te1/0/15-16) |
| Gi1/0/1-19    | USER_DATA    | Access | 10              | User Workstations              |
| Gi1/0/20-30   | USER_VOICE   | Access | 20              | VoIP Phones                    |
| Gi1/0/31-32   | AP_PORTS     | Trunk  | 30,40           | Wireless Access Points         |
| Gi1/0/48      | MGMT_PORT    | Access | 250             | Management Network             |

### Port Channel Configuration
- **Port-Channel 1**: Te1/0/1-2 (To MDF1)
  - **LACP Mode**: Active
  - **Description**: TO_MDF1_CORE
  - **Member Interfaces**:
    - Te1/0/1: To SIC-MDF1-CORE-01 (Te1/0/15)
    - Te1/0/2: To SIC-MDF1-CORE-01 (Te1/0/16)

- **Port-Channel 2**: Te1/0/3-4 (To MDF2)
  - **LACP Mode**: Active
  - **Description**: TO_MDF2_CORE
  - **Member Interfaces**:
    - Te1/0/3: To SIC-MDF2-CORE-01 (Te1/0/15)
    - Te1/0/4: To SIC-MDF2-CORE-01 (Te1/0/16)

### Spanning Tree Configuration
- **Mode**: Rapid-PVST+
- **Root Guard**: Enabled on access ports
- **BPDU Guard**: Enabled on access ports
- **Portfast**: Enabled on access ports