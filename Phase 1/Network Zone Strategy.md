## Overall Network Strategy 

| Network Zone       | VLAN ID | Subnet     | CIDR | Available IPs | Purpose                      |
| ------------------ | ------- | ---------- | ---- | ------------- | ---------------------------- |
| Management         | 250     | 10.0.250.0 | /26  | 62            | Network device management    |
| Network Services   | 200     | 10.0.200.0 | /26  | 62            | DHCP, DNS, VG, FSP servers   |
| SIC Data/Users     | 10      | 10.0.10.0  | /23  | 510           | General user data traffic    |
| SIC Voice          | 20      | 10.0.20.0  | /23  | 510           | Voice traffic                |
| SIC Wireless       | 32      | 10.0.32.0  | /22  | 1022          | Wireless client connectivity |
| SIC Guest Wireless | 40      | 10.0.40.0  | /22  | 1022          | Guest wireless access        |
| Finance Data/Users | 100     | 10.0.100.0 | /25  | 126           | Finance department data      |
| Finance Voice      | 110     | 10.0.110.0 | /25  | 126           | Finance department voice     |
| FSP Transit        | 150     | 10.0.150.0 | /29  | 6             | FSP to Core transit network  |
| Routing            | 254     | 10.0.254.0 | /30  | 2             | MDF-MDF routing              |
Clear easy to see breakdown
## 2. MDF1 IP Addresses 

|Network Zone|VLAN ID|IP Address|Subnet Mask|Default Gateway|
|---|---|---|---|---|
|Management|250|10.0.250.2|255.255.255.192|10.0.250.1|
|Network Services|200|10.0.200.2|255.255.255.192|10.0.200.1|
|SIC Data/Users|10|10.0.10.2|255.255.254.0|10.0.10.1|
|SIC Voice|20|10.0.20.2|255.255.254.0|10.0.20.1|
|SIC Wireless|32|10.0.32.2|255.255.252.0|10.0.32.1|
|SIC Guest Wireless|40|10.0.40.2|255.255.252.0|10.0.40.1|
|Finance Data/Users|100|10.0.100.2|255.255.255.128|10.0.100.1|
|Finance Voice|110|10.0.110.2|255.255.255.128|10.0.110.1|
|Routing|254|10.0.254.2|255.255.255.252|N/A|

## 3. MDF2 IP Addresses 

|Network Zone|VLAN ID|IP Address|Subnet Mask|Default Gateway|
|---|---|---|---|---|
|Management|250|10.0.250.3|255.255.255.192|10.0.250.1|
|Network Services|200|10.0.200.3|255.255.255.192|10.0.200.1|
|SIC Data/Users|10|10.0.10.3|255.255.254.0|10.0.10.1|
|SIC Voice|20|10.0.20.3|255.255.254.0|10.0.20.1|
|SIC Wireless|32|10.0.32.3|255.255.252.0|10.0.32.1|
|SIC Guest Wireless|40|10.0.40.3|255.255.252.0|10.0.40.1|
|Finance Data/Users|100|10.0.100.3|255.255.255.128|10.0.100.1|
|Finance Voice|110|10.0.110.3|255.255.255.128|10.0.110.1|
|Routing|254|10.0.254.3|255.255.255.252|N/A|

## 4. High Availability Virtual IP Addresses (VIPs)

|Network Zone|VLAN ID|VIP Address|Subnet Mask|Purpose|
|---|---|---|---|---|
|Management|250|10.0.250.1|255.255.255.192|Default gateway for management devices|
|Network Services|200|10.0.200.1|255.255.255.192|Default gateway for servers|
|SIC Data/Users|10|10.0.10.1|255.255.254.0|Default gateway for user workstations|
|SIC Voice|20|10.0.20.1|255.255.254.0|Default gateway for voice devices|
|SIC Wireless|32|10.0.32.1|255.255.252.0|Default gateway for wireless clients|
|SIC Guest Wireless|40|10.0.40.1|255.255.252.0|Default gateway for guest wireless|
|Finance Data/Users|100|10.0.100.1|255.255.255.128|Default gateway for finance workstations|
|Finance Voice|110|10.0.110.1|255.255.255.128|Default gateway for finance voice devices|


##  Overall Network Strategy

| Network Zone       | VLAN ID | Subnet     | CIDR | Available IPs | Purpose                      |
| ------------------ | ------- | ---------- | ---- | ------------- | ---------------------------- |
| Management         | 250     | 10.0.250.0 | /26  | 62            | Network device management    |
| Network Services   | 200     | 10.0.200.0 | /26  | 62            | DHCP, DNS, VG, FSP servers   |
| SIC Data/Users     | 10      | 10.0.10.0  | /23  | 510           | General user data traffic    |
| SIC Voice          | 20      | 10.0.20.0  | /23  | 510           | Voice traffic                |
| SIC Wireless       | 32      | 10.0.32.0  | /22  | 1022          | Wireless client connectivity |
| SIC Guest Wireless | 40      | 10.0.40.0  | /22  | 1022          | Guest wireless access        |
| Finance Data/Users | 100     | 10.0.100.0 | /25  | 126           | Finance department data      |
| Finance Voice      | 110     | 10.0.110.0 | /25  | 126           | Finance department voice     |
| FSP Transit        | 150     | 10.0.150.0 | /29  | 6             | FSP to Core transit network  |
| Routing            | 254     | 10.0.254.0 | /30  | 2             | MDF-MDF routing              |

##  Specific Finance Security Proxy IP Requirements

Here are the specific IPs requested for the Finance Security Proxy configuration:

### Finance Inside Interfaces

- **2 Finance Data IPs (VLAN 100)**:
    - FN-FW1 Finance Data IP: **10.0.100.5/25**
    - FN-FW2 Finance Data IP: **10.0.100.6/25**
- **2 Finance Voice IPs (VLAN 110)**:
    - FN-FW1 Finance Voice IP: **10.0.110.5/25**
    - FN-FW2 Finance Voice IP: **10.0.110.6/25**
- **Finance Default Gateway IPs**:
    - Finance Data Default Gateway IP: **10.0.100.1/25** (FSP VIP)
    - Finance Voice Default Gateway IP: **10.0.110.1/25** (FSP VIP)

### Finance Outside Interfaces (Transit Network)

- **2 IPs for outside interfaces (VLAN 150)**:
    - FN-FW1 Outside Interface: **10.0.150.5/29**
    - FN-FW2 Outside Interface: **10.0.150.6/29**
- **VIP IP for next-hop routing (VLAN 150)**:
    - FSP Outside VIP: **10.0.150.4/29**
- **Next-hop IP for default route to primary network**:
    - Core Transit VLAN VIP: **10.0.150.1/29** (for FSP's default route)

## Core Switch Transit Interface IPs (VLAN 150)

- MDF1 Transit Interface: **10.0.150.2/29**
- MDF2 Transit Interface: **10.0.150.3/29**
- Transit Network VIP: **10.0.150.1/29**

## Network Services Server IP Clusters

Regarding the Network Services IPs (from your previous question), each service requires 3 IPs for clustering:

### DHCP Servers (VLAN 200)

- MDF1 DHCP Server: **10.0.200.11/26**
- MDF2 DHCP Server: **10.0.200.12/26**
- DHCP VIP: **10.0.200.10/26**

### DNS Servers (VLAN 200)

- MDF1 DNS Server: **10.0.200.21/26**
- MDF2 DNS Server: **10.0.200.22/26**
- DNS VIP: **10.0.200.20/26**

### Voice Gateway Servers (VLAN 200)

- MDF1 Voice Gateway: **10.0.200.31/26**
- MDF2 Voice Gateway: **10.0.200.32/26**
- Voice Gateway VIP: **10.0.200.30/26**

### Financial Services Proxy (FSP) Addresses

These are the physical servers hosting network services (not to be confused with the Finance Security Proxy firewalls):

- MDF1 FSP Server: **10.0.200.41/26**
- MDF2 FSP Server: **10.0.200.42/26**
- FSP Server VIP: **10.0.200.40/26**


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