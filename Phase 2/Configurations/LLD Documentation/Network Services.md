# Network Services Configuration
#tags: #network #services #DHCP #DNS #VoIP #LLD #phase2

## DHCP Services
### Primary DHCP Server (MDF1)
- **Physical IP**: 10.0.200.11/26
- **Virtual IP (VIP)**: 10.0.200.101/26
- **Host Name**: SIC-MDF1-DHCP
- **Connected To**: SIC-MDF1-CORE-01 (Port-channel21)
- **High Availability Mode**: Active-Active with load balancing

#### Network Interfaces
| Interface | Description | Connected To | VLAN | IP Address |
|-----------|-------------|--------------|------|------------|
| Bond0 (eth0-1) | Core Switch Connection | SIC-MDF1-CORE-01 (Gi1/0/1-2) | 200 | 10.0.200.11/26 |
| eth0 | Primary Network | SIC-MDF1-CORE-01 (Gi1/0/1) | 200 | N/A (Bond member) |
| eth1 | Secondary Network | SIC-MDF1-CORE-01 (Gi1/0/2) | 200 | N/A (Bond member) |

### Secondary DHCP Server (MDF2)
- **Physical IP**: 10.0.200.12/26
- **Virtual IP (VIP)**: 10.0.200.101/26
- **Host Name**: SIC-MDF2-DHCP
- **Connected To**: SIC-MDF2-CORE-01 (Port-channel21)
- **High Availability Mode**: Active-Active with load balancing

#### Network Interfaces
| Interface | Description | Connected To | VLAN | IP Address |
|-----------|-------------|--------------|------|------------|
| Bond0 (eth0-1) | Core Switch Connection | SIC-MDF2-CORE-01 (Gi1/0/1-2) | 200 | 10.0.200.12/26 |
| eth0 | Primary Network | SIC-MDF2-CORE-01 (Gi1/0/1) | 200 | N/A (Bond member) |
| eth1 | Secondary Network | SIC-MDF2-CORE-01 (Gi1/0/2) | 200 | N/A (Bond member) |

### DHCP Scopes
| VLAN/Network | Scope Range | Lease Time | Default Gateway | DNS Servers | Exclusions |
|--------------|-------------|------------|-----------------|-------------|------------|
| VLAN 10 (Data) | 10.0.10.50-10.0.11.254 | 8 hours | 10.0.10.1 | 10.0.200.102 | 10.0.10.1-10.0.10.49 |
| VLAN 20 (Voice) | 10.0.20.50-10.0.21.254 | 8 hours | 10.0.20.1 | 10.0.200.102 | 10.0.20.1-10.0.20.49 |
| VLAN 30 (Wireless) | 10.0.30.50-10.0.33.254 | 4 hours | 10.0.30.1 | 10.0.200.102 | 10.0.30.1-10.0.30.49 |
| VLAN 40 (Guest) | 10.0.40.50-10.0.43.254 | 1 hour | 10.0.40.1 | 10.0.200.102 | 10.0.40.1-10.0.40.49 |
| VLAN 100 (Finance) | 10.0.100.50-10.0.100.126 | 8 hours | 10.0.100.1 | 10.0.200.102 | 10.0.100.1-10.0.100.49 |
| VLAN 110 (Finance Voice) | 10.0.110.50-10.0.110.126 | 8 hours | 10.0.110.1 | 10.0.200.102 | 10.0.110.1-10.0.110.49 |
| VLAN 250 (Management) | 10.0.250.50-10.0.250.62 | 24 hours | 10.0.250.1 | 10.0.200.102 | 10.0.250.1-10.0.250.49 |

## DNS Services
### Primary DNS Server (MDF1)
- **Physical IP**: 10.0.200.21/26
- **Virtual IP (VIP)**: 10.0.200.102/26
- **Host Name**: SIC-MDF1-DNS
- **Connected To**: SIC-MDF1-CORE-01 (Port-channel22)
- **High Availability Mode**: Active-Active with load balancing

#### Network Interfaces
| Interface | Description | Connected To | VLAN | IP Address |
|-----------|-------------|--------------|------|------------|
| Bond0 (eth0-1) | Core Switch Connection | SIC-MDF1-CORE-01 (Gi1/0/3-4) | 200 | 10.0.200.21/26 |
| eth0 | Primary Network | SIC-MDF1-CORE-01 (Gi1/0/3) | 200 | N/A (Bond member) |
| eth1 | Secondary Network | SIC-MDF1-CORE-01 (Gi1/0/4) | 200 | N/A (Bond member) |

### Secondary DNS Server (MDF2)
- **Physical IP**: 10.0.200.22/26
- **Virtual IP (VIP)**: 10.0.200.102/26
- **Host Name**: SIC-MDF2-DNS
- **Connected To**: SIC-MDF2-CORE-01 (Port-channel22)
- **High Availability Mode**: Active-Active with load balancing

#### Network Interfaces
| Interface | Description | Connected To | VLAN | IP Address |
|-----------|-------------|--------------|------|------------|
| Bond0 (eth0-1) | Core Switch Connection | SIC-MDF2-CORE-01 (Gi1/0/3-4) | 200 | 10.0.200.22/26 |
| eth0 | Primary Network | SIC-MDF2-CORE-01 (Gi1/0/3) | 200 | N/A (Bond member) |
| eth1 | Secondary Network | SIC-MDF2-CORE-01 (Gi1/0/4) | 200 | N/A (Bond member) |

### DNS Zone Configuration
- **Primary Zone**: seaice.local
- **Reverse Lookup Zones**: 10.0.0.0/8
- **Record Synchronization**: Automatic between primary and secondary

## Voice Gateway Services
### Primary Voice Gateway (MDF1)
- **Physical IP**: 10.0.200.31/26
- **Virtual IP (VIP)**: 10.0.200.103/26
- **Host Name**: SIC-MDF1-VG
- **Connected To**: SIC-MDF1-CORE-01 (Port-channel23)
- **High Availability Mode**: Active-Passive

#### Network Interfaces
| Interface | Description | Connected To | VLAN | IP Address |
|-----------|-------------|--------------|------|------------|
| Port-channel1 | Core Switch Connection | SIC-MDF1-CORE-01 (Gi1/0/5-6) | 200 | 10.0.200.31/26 |
| GigabitEthernet0/0 | Primary Network | SIC-MDF1-CORE-01 (Gi1/0/5) | 200 | N/A (Port-channel member) |
| GigabitEthernet0/1 | Secondary Network | SIC-MDF1-CORE-01 (Gi1/0/6) | 200 | N/A (Port-channel member) |

### Secondary Voice Gateway (MDF2)
- **Physical IP**: 10.0.200.32/26
- **Virtual IP (VIP)**: 10.0.200.103/26
- **Host Name**: SIC-MDF2-VG
- **Connected To**: SIC-MDF2-CORE-01 (Port-channel23)
- **High Availability Mode**: Active-Passive

#### Network Interfaces
| Interface | Description | Connected To | VLAN | IP Address |
|-----------|-------------|--------------|------|------------|
| Port-channel1 | Core Switch Connection | SIC-MDF2-CORE-01 (Gi1/0/5-6) | 200 | 10.0.200.32/26 |
| GigabitEthernet0/0 | Primary Network | SIC-MDF2-CORE-01 (Gi1/0/5) | 200 | N/A (Port-channel member) |
| GigabitEthernet0/1 | Secondary Network | SIC-MDF2-CORE-01 (Gi1/0/6) | 200 | N/A (Port-channel member) |

### Voice Gateway Configuration
- **SIP Proxy**: Enabled
- **Call Manager Integration**: Configured
- **Dial Plan**: North American
- **Voice VLAN Support**: VLAN 20 (Regular), VLAN 110 (Finance)

## Financial Services Proxy (FSP)
### Primary FSP Server (MDF1)
- **Physical IP**: 10.0.200.41/26 (Internal)
- **Transit Interface IP**: 10.0.150.14/29 (Transit)
- **Virtual IP (VIP)**: 10.0.200.104/26 (Internal)
- **Transit Virtual IP**: 10.0.150.9/29 (Transit)
- **Host Name**: SIC-MDF1-FSP
- **Connected To**: SIC-MDF1-CORE-01 (Gi1/0/4)
- **High Availability Mode**: Active-Passive

### Secondary FSP Server (MDF2)
- **Physical IP**: 10.0.200.42/26 (Internal)
- **Transit Interface IP**: 10.0.150.15/29 (Transit)
- **Virtual IP (VIP)**: 10.0.200.104/26 (Internal)
- **Transit Virtual IP**: 10.0.150.9/29 (Transit)
- **Host Name**: SIC-MDF2-FSP
- **Connected To**: SIC-MDF2-CORE-01 (Gi1/0/4)
- **High Availability Mode**: Active-Passive