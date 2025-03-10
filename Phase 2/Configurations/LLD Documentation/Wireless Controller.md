# Wireless Controllers Configuration
#tags: #network #wireless #WLC #LLD #phase2 #cisco

## SIC-MDF1-WLC-01 (Primary Wireless Controller)
- **Management IP**: 10.0.250.41/26
- **Default Gateway**: 10.0.250.1 (Virtual IP)
- **Service Port IP**: 10.0.250.141/26
- **Redundancy Mode**: SSO (Stateful Switchover)
- **Redundancy Role**: Primary
- **Mobility Domain Name**: SIC-MOBILITY

### Interface Configuration
| Interface | VLAN | IP Address | Subnet Mask | Purpose |
|-----------|------|------------|-------------|---------|
| Management | 250 | 10.0.250.41 | 255.255.255.192 | Controller Management |
| Corporate_WLAN | 30 | 10.0.30.31 | 255.255.252.0 | Corporate Wireless Clients |
| Guest_WLAN | 40 | 10.0.40.31 | 255.255.252.0 | Guest Wireless Clients |
| Service-Port | N/A | 10.0.250.141 | 255.255.255.192 | Direct Console Access |
| LAG-1 | N/A | N/A | N/A | Link Aggregation Group |

### Port Channel Configuration
- **LAG-1**:
  - **LACP Mode**: Active
  - **Port Members**:
    - port1: Connected to SIC-MDF1-CORE-01 (Gi1/0/7)
    - port2: Connected to SIC-MDF1-CORE-01 (Gi1/0/8)
  - **Mode**: 802.3ad
  - **Interface Type**: Trunk
  - **VLANs Allowed**: 30,40,250

### WLAN Configuration
| WLAN Name | SSID | Interface | Security | QoS |
|-----------|------|-----------|----------|-----|
| SIC-Corporate | SIC-Corp | Corporate_WLAN | WPA2-Enterprise (802.1X) | Platinum |
| SIC-Guest | SIC-Guest | Guest_WLAN | WPA2-PSK | Bronze |

### RF Profile
- **802.11a/n/ac Profile**:
  - **Name**: SIC-5GHz
  - **Channel Width**: 40 MHz
  - **DFS Channels**: Enabled
  - **Power Level**: Auto (Range 1-5)

- **802.11b/g/n Profile**:
  - **Name**: SIC-2.4GHz
  - **Channel Width**: 20 MHz
  - **Power Level**: Auto (Range 1-5)

### High Availability
- **Redundancy Port**: Enabled
- **Peer IP**: 10.0.250.42 (Secondary WLC)
- **Keep Alive Timer**: 100 ms
- **Peer Detection Timer**: 300 ms

## SIC-MDF2-WLC-01 (Secondary Wireless Controller)
- **Management IP**: 10.0.250.42/26
- **Default Gateway**: 10.0.250.1 (Virtual IP)
- **Service Port IP**: 10.0.250.142/26
- **Redundancy Mode**: SSO (Stateful Switchover)
- **Redundancy Role**: Secondary
- **Mobility Domain Name**: SIC-MOBILITY

### Interface Configuration
| Interface | VLAN | IP Address | Subnet Mask | Purpose |
|-----------|------|------------|-------------|---------|
| Management | 250 | 10.0.250.42 | 255.255.255.192 | Controller Management |
| Corporate_WLAN | 30 | 10.0.30.32 | 255.255.252.0 | Corporate Wireless Clients |
| Guest_WLAN | 40 | 10.0.40.32 | 255.255.252.0 | Guest Wireless Clients |
| Service-Port | N/A | 10.0.250.142 | 255.255.255.192 | Direct Console Access |
| LAG-1 | N/A | N/A | N/A | Link Aggregation Group |

### Port Channel Configuration
- **LAG-1**:
  - **LACP Mode**: Active
  - **Port Members**:
    - port1: Connected to SIC-MDF2-CORE-01 (Gi1/0/7)
    - port2: Connected to SIC-MDF2-CORE-01 (Gi1/0/8)
  - **Mode**: 802.3ad
  - **Interface Type**: Trunk
  - **VLANs Allowed**: 30,40,250

### WLAN Configuration
| WLAN Name | SSID | Interface | Security | QoS |
|-----------|------|-----------|----------|-----|
| SIC-Corporate | SIC-Corp | Corporate_WLAN | WPA2-Enterprise (802.1X) | Platinum |
| SIC-Guest | SIC-Guest | Guest_WLAN | WPA2-PSK | Bronze |

### RF Profile
- Same as Primary WLC

### High Availability
- **Redundancy Port**: Enabled
- **Peer IP**: 10.0.250.41 (Primary WLC)
- **Keep Alive Timer**: 100 ms
- **Peer Detection Timer**: 300 ms