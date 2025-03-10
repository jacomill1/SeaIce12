# Developing a Network Device Naming Convention for SeaIce Creamery

#networking #LLD #naming #standards #documentation #Phase2 #interfaces #ports

A consistent, logical naming convention is crucial for your SeaIce Creamery network. Your suggested approach (sic-idf-wgs-1) is on the right track, but we can refine it to be even more comprehensive and scalable.

## Recommended Naming Convention

I recommend a hierarchical naming structure following this pattern:

```
SIC-[LOCATION]-[DEVICE TYPE]-[NUMBER]
```

### Components:

1. **SIC** - Organization prefix (SeaIce Creamery)
2. **LOCATION** - Where the device is physically located (MDF1, MDF2, IDF1, IDF2, etc.)
3. **DEVICE TYPE** - Function of the device using standardized abbreviations:
    - CORE: Core switches
    - ACC: Access switches
    - FW: Edge firewalls
    - FNFW: Finance firewalls
    - WLC: Wireless controllers
    - AP: Access points
    - RTR: Routers (if needed in future)
4. **NUMBER** - Sequential number (01, 02, etc.) for multiple devices of the same type

### Examples:

#### Core Infrastructure

- **SIC-MDF1-CORE-01**: Core switch in MDF1
- **SIC-MDF2-CORE-01**: Core switch in MDF2

#### Access Switches

- **SIC-IDF1-ACC-01**: Access switch 1 in IDF1
- **SIC-IDF2-ACC-01**: Access switch 1 in IDF2
- **SIC-IDF3-ACC-02**: Second access switch in IDF3

#### Firewalls

- **SIC-MDF1-FW-01**: Edge firewall 1 in MDF1
- **SIC-MDF2-FW-01**: Edge firewall 1 in MDF2
- **SIC-MDF1-FNFW-01**: Finance firewall 1 in MDF1
- **SIC-MDF2-FNFW-01**: Finance firewall 2 in MDF2

#### Wireless

- **SIC-MDF1-WLC-01**: Wireless controller in MDF1
- **SIC-IDF1-AP-01**: Access point 1 in IDF1 area
- **SIC-IDF2-AP-03**: Access point 3 in IDF2 area

### For Interface Naming

You can extend this convention to interface names:

- Physical interfaces: `[Device Name]-[Interface Type][Module/Slot]/[Port]` Example: `SIC-MDF1-CORE-01-Gi1/0/1`
- Logical interfaces: `[Device Name]-[Interface Type][ID]` Example: `SIC-MDF1-CORE-01-VLAN10`

## Benefits of This Convention

1. **Easy Location Identification**: Location is immediately apparent from the name
2. **Clear Device Function**: Device type is standardized and clearly indicated
3. **Simple Inventory Management**: Sequential numbering helps with asset tracking
4. **Scalability**: Convention can accommodate network growth
5. **Consistency**: Format works across all device types
6. **Troubleshooting Efficiency**: Names indicate both location and function, speeding up issue resolution

This naming convention provides all the information needed for efficient network management while staying concise enough for practical use.


# The Names

## SIC-MDF1-FW-01 (Primary Edge Firewall)
| Interface | Connected To            | Purpose                   | VLAN/Subnet               | Notes                               |
| --------- | ----------------------- | ------------------------- | ------------------------- | ----------------------------------- |
| g1/1      | Century Link            | WAN Connection            | ISP Assigned              | Primary Internet Connetion          |
| g1/2      | SIC-MDF1-CORE-01 (g0/1) | Core Connectivity         | VLAN 150 (10.0.150.5/29)  | Primary Connection to Core Switch 1 |
| g1/3      | SIC-MDF2-CORE-01 (G0/1) | Redundant Core Connection | VLAN 150 (10.0.150.5/29)  | Fore Core Redundancy                |
| g1/4      | SIC-MDF2-FW-01 (g1/4)   | HA1 Link                  | N/A                       | Control Sync                        |
| g1/5      | SIC-MDF2-FW-01 (g1/5)   | HA2 Link                  | N/A                       | Session State Synch                 |
| MGMT      | Management Network      | Out-Of-Band Mgmt          | Vlan 250 (10.0.250.11/26) | Dedicated Management                |
### SIC-MDF2-FW-01 (Secondary Edge Firewall)

| Interface | Connected To               | Purpose                | VLAN/Subnet               | Notes                         |
| --------- | -------------------------- | ---------------------- | ------------------------- | ----------------------------- |
| eth1/1    | Comcast                    | WAN Connection         | ISP Assigned              | Secondary Internet connection |
| eth1/2    | SIC-MDF2-CORE-01 (Te1/0/1) | Core Connectivity      | VLAN 150 (10.0.150.6/29)  | Transit network to core       |
| eth1/3    | SIC-MDF1-CORE-01 (Te1/0/2) | Redundant Core Link    | VLAN 150 (10.0.150.6/29)  | For core switch redundancy    |
| eth1/4    | SIC-MDF1-FW-01 eth1/4      | HA1 Link               | N/A                       | Control plane synchronization |
| eth1/5    | SIC-MDF1-FW-01 eth1/5      | HA2 Link               | N/A                       | Session state synchronization |
| MGT       | Management Network         | Out-of-band management | VLAN 250 (10.0.250.12/26) | Dedicated management          |


##  SIC-MDF1-CORE-01
| Interface   | Connected To               | Purpose                          | VLAN/Subnet   | Configuration                 |
| ----------- | -------------------------- | -------------------------------- | ------------- | ----------------------------- |
| Te1/0/1     | SIC-MDF1-FW-01 eth1/2      | Edge Firewall Connection         | VLAN 150      | Trunk (VLAN 150)              |
| Te1/0/2     | SIC-MDF2-FW-01 eth1/3      | Redundant Edge FW Link           | VLAN 150      | Trunk (VLAN 150)              |
| Te1/0/3     | SIC-MDF1-FNFW-01 eth1/2    | Finance FW Connection            | VLAN 150      | Trunk (VLAN 150)              |
| Te1/0/4     | SIC-MDF2-FNFW-01 eth1/3    | Redundant Finance FW Link        | VLAN 150      | Trunk (VLAN 150)              |
| Te1/0/5-8   | SIC-MDF2-CORE-01 Te1/0/5-8 | Core Interconnect (Port Channel) | All VLANs     | Trunk (All VLANs)             |
| Te1/0/9-10  | SIC-IDF1-ACC-01 Te1/0/1-2  | IDF1 Uplink (Port Channel)       | User VLANs    | Trunk (VLANs 10,20,32,40,250) |
| Te1/0/11-12 | SIC-IDF2-ACC-01 Te1/0/1-2  | IDF2 Uplink (Port Channel)       | Finance VLANs | Trunk (VLANs 100,110,250)     |
| Te1/0/13-14 | SIC-IDF3-ACC-01 Te1/0/1-2  | IDF3 Uplink (Port Channel)       | User VLANs    | Trunk (VLANs 10,20,32,40,250) |
| Te1/0/15-16 | SIC-IDF4-ACC-01 Te1/0/1-2  | IDF4 Uplink (Port Channel)       | User VLANs    | Trunk (VLANs 10,20,32,40,250) |
| Gi1/0/1-4   | DHCP/DNS/VG/FSP Servers    | Network Services                 | VLAN 200      | Access (VLAN 200)             |
| Gi1/0/5     | SIC-MDF1-WLC-01            | Wireless Controller              | VLAN 32       | Trunk (VLANs 32,40,250)       |
| Gi1/0/24    | Management Network         | OOB Management                   | VLAN 250      | Access (VLAN 250)             |
## SIC-MDF2-Core-01
| Interface   | Connected To               | Purpose                          | VLAN/Subnet   | Configuration                 |
| ----------- | -------------------------- | -------------------------------- | ------------- | ----------------------------- |
| Te1/0/1     | SIC-MDF2-FW-01 eth1/2      | Edge Firewall Connection         | VLAN 150      | Trunk (VLAN 150)              |
| Te1/0/2     | SIC-MDF1-FW-01 eth1/3      | Redundant Edge FW Link           | VLAN 150      | Trunk (VLAN 150)              |
| Te1/0/3     | SIC-MDF2-FNFW-01 eth1/2    | Finance FW Connection            | VLAN 150      | Trunk (VLAN 150)              |
| Te1/0/4     | SIC-MDF1-FNFW-01 eth1/3    | Redundant Finance FW Link        | VLAN 150      | Trunk (VLAN 150)              |
| Te1/0/5-8   | SIC-MDF1-CORE-01 Te1/0/5-8 | Core Interconnect (Port Channel) | All VLANs     | Trunk (All VLANs)             |
| Te1/0/9-10  | SIC-IDF1-ACC-01 Te1/0/3-4  | IDF1 Uplink (Port Channel)       | User VLANs    | Trunk (VLANs 10,20,32,40,250) |
| Te1/0/11-12 | SIC-IDF2-ACC-01 Te1/0/3-4  | IDF2 Uplink (Port Channel)       | Finance VLANs | Trunk (VLANs 100,110,250)     |
| Te1/0/13-14 | SIC-IDF3-ACC-01 Te1/0/3-4  | IDF3 Uplink (Port Channel)       | User VLANs    | Trunk (VLANs 10,20,32,40,250) |
| Te1/0/15-16 | SIC-IDF4-ACC-01 Te1/0/3-4  | IDF4 Uplink (Port Channel)       | User VLANs    | Trunk (VLANs 10,20,32,40,250) |
| Gi1/0/1-4   | DHCP/DNS/VG/FSP Servers    | Network Services                 | VLAN 200      | Access (VLAN 200)             |
| Gi1/0/5     | SIC-MDF2-WLC-01            | Wireless Controller              | VLAN 32       | Trunk (VLANs 32,40,250)       |
| Gi1/0/24    | Management Network         | OOB Management                   | VLAN 250      | Access (VLAN 250)             |

## Finance Firewalls Port Assignment

### SIC-MDF1-FNFW-01 (Primary Finance Firewall)

| Interface  | Connected To             | Purpose                | VLAN/Subnet               | Configuration         |
| ---------- | ------------------------ | ---------------------- | ------------------------- | --------------------- |
| eth1/1.100 | SIC-IDF2-ACC-01          | Finance Data Inside    | VLAN 100 (10.0.100.5/25)  | Subinterface VLAN 100 |
| eth1/1.110 | SIC-IDF2-ACC-01          | Finance Voice Inside   | VLAN 110 (10.0.110.5/25)  | Subinterface VLAN 110 |
| eth1/2     | SIC-MDF1-CORE-01 Te1/0/3 | Outside Interface      | VLAN 150 (10.0.150.5/29)  | Trunk (VLAN 150)      |
| eth1/3     | SIC-MDF2-CORE-01 Te1/0/3 | Redundant Outside Link | VLAN 150 (10.0.150.5/29)  | Trunk (VLAN 150)      |
| eth1/4     | SIC-MDF2-FNFW-01 eth1/4  | HA1 Link               | N/A                       | Control plane sync    |
| eth1/5     | SIC-MDF2-FNFW-01 eth1/5  | HA2 Link               | N/A                       | Session state sync    |
| MGT        | Management Network       | OOB Management         | VLAN 250 (10.0.250.21/26) | Dedicated management  |


### SIC-MDF2-FNFW-01 (Secondary Finance Firewall)

|Interface|Connected To|Purpose|VLAN/Subnet|Configuration|
|---|---|---|---|---|
|eth1/1.100|SIC-IDF2-ACC-01|Finance Data Inside|VLAN 100 (10.0.100.6/25)|Subinterface VLAN 100|
|eth1/1.110|SIC-IDF2-ACC-01|Finance Voice Inside|VLAN 110 (10.0.110.6/25)|Subinterface VLAN 110|
|eth1/2|SIC-MDF2-CORE-01 Te1/0/3|Outside Interface|VLAN 150 (10.0.150.6/29)|Trunk (VLAN 150)|
|eth1/3|SIC-MDF1-CORE-01 Te1/0/4|Redundant Outside Link|VLAN 150 (10.0.150.6/29)|Trunk (VLAN 150)|
|eth1/4|SIC-MDF1-FNFW-01 eth1/4|HA1 Link|N/A|Control plane sync|
|eth1/5|SIC-MDF1-FNFW-01 eth1/5|HA2 Link|N/A|Session state sync|
|MGT|Management Network|OOB Management|VLAN 250 (10.0.250.22/26)|Dedicated management|

