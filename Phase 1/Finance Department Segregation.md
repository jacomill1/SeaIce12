## Finance Department Isolation Implementation

The Finance Department isolation design remains the same conceptually, just with the updated subnet addresses:

1. **VLAN Isolation**: Finance uses separate VLANs (100, 101) restricted to IDF2.
2. **ACL Implementation:** 

```
! Block direct Finance to SIC communication
access-list 101 deny ip 10.100.0.0 0.0.255.255 10.10.0.0 0.0.255.255
access-list 101 deny ip 10.100.0.0 0.0.255.255 10.20.0.0 0.0.255.255  
access-list 101 deny ip 10.100.0.0 0.0.255.255 10.30.0.0 0.0.255.255

! Allow Finance to FSP communication only
access-list 101 permit ip 10.100.0.0 0.0.255.255 host 10.2.0.40  ! FSP VIP
access-list 101 permit ip 10.100.0.0 0.0.255.255 host 10.2.0.41  ! FSP MDF1
access-list 101 permit ip 10.100.0.0 0.0.255.255 host 10.2.0.42  ! FSP MDF2

! Deny any other Finance outbound traffic
access-list 101 deny ip 10.100.0.0 0.0.255.255 any
```

1. Traffic Flow: Should remain the same.



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