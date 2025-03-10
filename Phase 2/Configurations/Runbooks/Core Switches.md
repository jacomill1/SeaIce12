# SeaIce MDF Core 1

```
!=====================================================
!
!  ____            _           ____             __ _       
! / ___|  ___  __ | |_ __  ___/ ___|___  _ __  / _(_) __ _ 
! \___ \ / _ \/ _|| | '_ \/ _ \___ \/ _ \| '_ \| |_| |/ _` |
!  ___) |  __/ (_ | | | | (_) |__) | (_) | | | |  _| | (_| |
! |____/ \___|\__||_|_| |_\___/____/ \___/|_| |_|_| |_|\__, |
!                                                      |___/ 
!=====================================================
enable
conf t
hostname SIC-MDF1-CORE-01
username admin secret SeaIce@dmin123!
enable secret SeaIce@dmin456!
!
! Configure console and VTY lines
line console 0
 logging synchronous
 login local
line vty 0 15
 logging synchronous
 login local
!
! Set IP domain and generate SSH keys
ip domain-name seaice.local
!
!=====================================================
! Loopback Configuration
!=====================================================
interface loopback0
 ip address 10.0.250.50 255.255.255.255
 description MGMT_LOOPBACK
exit
!
!=====================================================
! OSPF Configuration
!=====================================================
router ospf 1
 router-id 10.0.250.50
 passive-interface default
 network 10.0.10.0 0.0.1.255 area 0 ! Data VLAN
 network 10.0.20.0 0.0.1.255 area 0 ! Voice VLAN
 network 10.0.30.0 0.0.3.255 area 0 ! Wireless VLAN
 network 10.0.40.0 0.0.3.255 area 0 ! Guest Wireless VLAN
 network 10.0.100.0 0.0.0.127 area 0 ! Finance Data VLAN
 network 10.0.110.0 0.0.0.127 area 0 ! Finance Voice VLAN
 network 10.0.150.0 0.0.0.7 area 0 ! Transit VLAN
 network 10.0.200.0 0.0.0.63 area 0 ! Network Services VLAN
 network 10.0.250.0 0.0.0.63 area 0 ! Management VLAN
 network 10.0.254.0 0.0.0.3 area 0 ! MDF Routing VLAN
!
! Remove passive interface for Core-to-Core communication
 no passive-interface Vlan254
exit
!
!=====================================================
! VLAN Configuration
!=====================================================
vlan 10
 name SIC_DATA_USERS
exit
vlan 20
 name SIC_VOICE
exit
vlan 30
 name SIC_WIRELESS
exit
vlan 40
 name SIC_GUEST_WIRELESS
exit
vlan 100
 name FINANCE_DATA
exit
vlan 110
 name FINANCE_VOICE
exit
vlan 150
 name FSP_TRANSIT
exit
vlan 200
 name NETWORK_SERVICES
exit
vlan 250
 name MANAGEMENT
exit
vlan 254
 name MDF_ROUTING
exit
!
!=====================================================
! SVI Configuration
!=====================================================
interface Vlan10
 description DATA_USERS
 ip address 10.0.10.2 255.255.254.0
 standby version 2
 standby 10 ip 10.0.10.1
 standby 10 priority 110
 standby 10 preempt delay minimum 180
 ip helper-address 10.0.200.101
 no shutdown
exit
!
interface Vlan20
 description VOICE
 ip address 10.0.20.2 255.255.254.0
 standby version 2
 standby 20 ip 10.0.20.1
 standby 20 priority 90
 standby 20 preempt
 ip helper-address 10.0.200.101
 no shutdown
exit
!
interface Vlan30
 description WIRELESS
 ip address 10.0.30.2 255.255.252.0
 standby version 2
 standby 30 ip 10.0.30.1
 standby 30 priority 110
 standby 30 preempt delay minimum 180
 ip helper-address 10.0.200.101
 no shutdown
exit
!
interface Vlan40
 description GUEST_WIRELESS
 ip address 10.0.40.2 255.255.252.0
 standby version 2
 standby 40 ip 10.0.40.1
 standby 40 priority 90
 standby 40 preempt
 ip helper-address 10.0.200.101
 no shutdown
exit
!
interface Vlan100
 description FINANCE_DATA
 ip address 10.0.100.2 255.255.255.128
 standby version 2
 standby 100 ip 10.0.100.1
 standby 100 priority 110
 standby 100 preempt delay minimum 180
 ip helper-address 10.0.200.101
 no shutdown
exit
!
interface Vlan110
 description FINANCE_VOICE
 ip address 10.0.110.2 255.255.255.128
 standby version 2
 standby 110 ip 10.0.110.1
 standby 110 priority 90
 standby 110 preempt
 ip helper-address 10.0.200.101
 no shutdown
exit
!
interface Vlan150
 description FSP_TRANSIT
 ip address 10.0.150.2 255.255.255.248
 no shutdown
exit
!
interface Vlan200
 description NETWORK_SERVICES
 ip address 10.0.200.2 255.255.255.192
 standby version 2
 standby 200 ip 10.0.200.1
 standby 200 priority 110
 standby 200 preempt delay minimum 180
 no shutdown
exit
!
interface Vlan250
 description MANAGEMENT
 ip address 10.0.250.2 255.255.255.192
 standby version 2
 standby 250 ip 10.0.250.1
 standby 250 priority 90
 standby 250 preempt
 no shutdown
exit
!
interface Vlan254
 description MDF_ROUTING
 ip address 10.0.254.1 255.255.255.252
 no shutdown
exit
!
!=====================================================
! Edge Firewall Connections
!=====================================================
interface TenGigabitEthernet1/0/1
 description TO_MDF1_FW
 switchport trunk allowed vlan 10,20,30,40,150,250
 switchport mode trunk
 no shutdown
exit
!
interface TenGigabitEthernet1/0/2
 description TO_MDF2_FW
 switchport trunk allowed vlan 10,20,30,40,150,250
 switchport mode trunk
 no shutdown
exit
!
!=====================================================
! Finance Firewall Connections
!=====================================================
interface TenGigabitEthernet1/0/3
 description TO_MDF1_FNFW
 switchport trunk allowed vlan 150
 switchport mode trunk
 no shutdown
exit
!
interface TenGigabitEthernet1/0/4
 description TO_MDF2_FNFW
 switchport trunk allowed vlan 150
 switchport mode trunk
 no shutdown
exit
!
!=====================================================
! Core-to-Core Port Channel Configuration
!=====================================================
interface port-channel5
 description TO_MDF2_CORE
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan all
 no shutdown
exit
!
interface range TenGigabitEthernet1/0/5-8
 description TO_MDF2_CORE
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan all
 channel-group 5 mode active
 no shutdown
exit
!
!=====================================================
! IDF Access Switch Port Channel Configuration
!=====================================================
!
! IDF1 Port Channel
interface port-channel9
 description TO_IDF1_ACC
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40,250
 no shutdown
exit
!
interface range TenGigabitEthernet1/0/9-10
 description TO_IDF1_ACC
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40,250
 channel-group 9 mode active
 no shutdown
exit
!
! IDF3 Port Channel
interface port-channel13
 description TO_IDF3_ACC
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40,250
 no shutdown
exit
!
interface range TenGigabitEthernet1/0/13-14
 description TO_IDF3_ACC
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40,250
 channel-group 13 mode active
 no shutdown
exit
!
! IDF4 Port Channel
interface port-channel15
 description TO_IDF4_ACC
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40,250
 no shutdown
exit
!
interface range TenGigabitEthernet1/0/15-16
 description TO_IDF4_ACC
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40,250
 channel-group 15 mode active
 no shutdown
exit
!
!=====================================================
! Network Services Connections
!=====================================================
!
! DHCP Server Port Channel
interface port-channel21
 description TO_DHCP_SERVER
 switchport mode access
 switchport access vlan 200
 spanning-tree portfast
 no shutdown
exit
!
interface range GigabitEthernet1/0/1-2
 description TO_DHCP_SERVER
 switchport mode access
 switchport access vlan 200
 spanning-tree portfast
 channel-group 21 mode active
 no shutdown
exit
!
! DNS Server Port Channel
interface port-channel22
 description TO_DNS_SERVER
 switchport mode access
 switchport access vlan 200
 spanning-tree portfast
 no shutdown
exit
!
interface range GigabitEthernet1/0/3-4
 description TO_DNS_SERVER
 switchport mode access
 switchport access vlan 200
 spanning-tree portfast
 channel-group 22 mode active
 no shutdown
exit
!
! Voice Gateway Port Channel
interface port-channel23
 description TO_VG_SERVER
 switchport mode access
 switchport access vlan 200
 spanning-tree portfast
 no shutdown
exit
!
interface range GigabitEthernet1/0/5-6
 description TO_VG_SERVER
 switchport mode access
 switchport access vlan 200
 spanning-tree portfast
 channel-group 23 mode active
 no shutdown
exit
!
! WLC Port Channel
interface port-channel24
 description TO_WLC
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 30,40,250
 no shutdown
exit
!
interface range GigabitEthernet1/0/7-8
 description TO_WLC
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 30,40,250
 channel-group 24 mode active
 no shutdown
exit
!
! FSP Server
interface GigabitEthernet1/0/4
 description TO_FSP_SERVER
 switchport mode access
 switchport access vlan 200
 spanning-tree portfast
 no shutdown
exit
!
!=====================================================
! Management Network Connection
!=====================================================
interface GigabitEthernet1/0/24
 description TO_MGMT_NET
 switchport mode access
 switchport access vlan 250
 spanning-tree portfast
 spanning-tree bpduguard enable
 no shutdown
exit
!
!=====================================================
! Spanning Tree Configuration
!=====================================================
spanning-tree mode rapid-pvst
spanning-tree extend system-id
!
! Set as root bridge for primary VLANs
spanning-tree vlan 10,30,100,200 priority 4096
! Set as secondary root bridge for secondary VLANs
spanning-tree vlan 20,40,110,250 priority 8192
!
ntp server 10.0.200.21
!
end
write memory
!=====================================================
! End of Configuration
!=====================================================
```

# SeaIce MDF 2 Core Switch

```
!=====================================================
!
!  ____            _           ____             __ _       
! / ___|  ___  __ | |_ __  ___/ ___|___  _ __  / _(_) __ _ 
! \___ \ / _ \/ _|| | '_ \/ _ \___ \/ _ \| '_ \| |_| |/ _` |
!  ___) |  __/ (_ | | | | (_) |__) | (_) | | | |  _| | (_| |
! |____/ \___|\__||_|_| |_\___/____/ \___/|_| |_|_| |_|\__, |
!                                                      |___/ 
!=====================================================
enable
conf t
hostname SIC-MDF2-CORE-01
username admin privilege 15 secret SeaIce@dmin123!
enable secret SeaIce@dmin456!
service password-encryption
!
! Set clock and timezone
clock timezone PST -8
clock summer-time PDT recurring
!
! Configure console and VTY lines
line console 0
 logging synchronous
 login local
line vty 0 15
 logging synchronous
 login local
 transport input ssh
!
! Set IP domain and generate SSH keys
ip domain-name seaice.local
!
!=====================================================
! Loopback Configuration
!=====================================================
interface loopback0
 ip address 10.0.250.51 255.255.255.255
 description MGMT_LOOPBACK
exit
!
!=====================================================
! OSPF Configuration
!=====================================================
router ospf 1
 router-id 10.0.250.51
 passive-interface default
 network 10.0.10.0 0.0.1.255 area 0 ! Data VLAN
 network 10.0.20.0 0.0.1.255 area 0 ! Voice VLAN
 network 10.0.30.0 0.0.3.255 area 0 ! Wireless VLAN
 network 10.0.40.0 0.0.3.255 area 0 ! Guest Wireless VLAN
 network 10.0.100.0 0.0.0.127 area 0 ! Finance Data VLAN
 network 10.0.110.0 0.0.0.127 area 0 ! Finance Voice VLAN
 network 10.0.150.0 0.0.0.7 area 0 ! Transit VLAN
 network 10.0.200.0 0.0.0.63 area 0 ! Network Services VLAN
 network 10.0.250.0 0.0.0.63 area 0 ! Management VLAN
 network 10.0.254.0 0.0.0.3 area 0 ! MDF Routing VLAN
!
! Remove passive interface for Core-to-Core communication
 no passive-interface Vlan254
exit
!
!=====================================================
! VLAN Configuration
!=====================================================
vlan 10
 name SIC_DATA_USERS
exit
vlan 20
 name SIC_VOICE
exit
vlan 30
 name SIC_WIRELESS
exit
vlan 40
 name SIC_GUEST_WIRELESS
exit
vlan 100
 name FINANCE_DATA
exit
vlan 110
 name FINANCE_VOICE
exit
vlan 150
 name FSP_TRANSIT
exit
vlan 200
 name NETWORK_SERVICES
exit
vlan 250
 name MANAGEMENT
exit
vlan 254
 name MDF_ROUTING
exit
!
!=====================================================
! SVI Configuration
!=====================================================
interface Vlan10
 description DATA_USERS
 ip address 10.0.10.3 255.255.254.0
 standby version 2
 standby 10 ip 10.0.10.1
 standby 10 priority 90
 standby 10 preempt
 ip helper-address 10.0.200.101
 no shutdown
exit
!
interface Vlan20
 description VOICE
 ip address 10.0.20.3 255.255.254.0
 standby version 2
 standby 20 ip 10.0.20.1
 standby 20 priority 110
 standby 20 preempt delay minimum 180
 ip helper-address 10.0.200.101
 no shutdown
exit
!
interface Vlan30
 description WIRELESS
 ip address 10.0.30.3 255.255.252.0
 standby version 2
 standby 30 ip 10.0.30.1
 standby 30 priority 90
 standby 30 preempt
 ip helper-address 10.0.200.101
 no shutdown
exit
!
interface Vlan40
 description GUEST_WIRELESS
 ip address 10.0.40.3 255.255.252.0
 standby version 2
 standby 40 ip 10.0.40.1
 standby 40 priority 110
 standby 40 preempt delay minimum 180
 ip helper-address 10.0.200.101
 no shutdown
exit
!
interface Vlan100
 description FINANCE_DATA
 ip address 10.0.100.3 255.255.255.128
 standby version 2
 standby 100 ip 10.0.100.1
 standby 100 priority 90
 standby 100 preempt
 ip helper-address 10.0.200.101
 no shutdown
exit
!
interface Vlan110
 description FINANCE_VOICE
 ip address 10.0.110.3 255.255.255.128
 standby version 2
 standby 110 ip 10.0.110.1
 standby 110 priority 110
 standby 110 preempt delay minimum 180
 ip helper-address 10.0.200.101
 no shutdown
exit
!
interface Vlan150
 description FSP_TRANSIT
 ip address 10.0.150.3 255.255.255.248
 no shutdown
exit
!
interface Vlan200
 description NETWORK_SERVICES_SVI
 ip address 10.0.200.3 255.255.255.192
 standby version 2
 standby 200 ip 10.0.200.1
 standby 200 priority 90
 standby 200 preempt
 no shutdown
exit
!
interface Vlan250
 description MANAGEMENT
 ip address 10.0.250.3 255.255.255.192
 standby version 2
 standby 250 ip 10.0.250.1
 standby 250 priority 110
 standby 250 preempt delay minimum 180
 no shutdown
exit
!
interface Vlan254
 description MDF_ROUTING
 ip address 10.0.254.2 255.255.255.252
 no shutdown
exit
!
!=====================================================
! Edge Firewall Connections
!=====================================================
interface TenGigabitEthernet1/0/1
 description TO_MDF2_FW
 switchport trunk allowed vlan 10,20,30,40,150,250
 switchport mode trunk
 no shutdown
exit
!
interface TenGigabitEthernet1/0/2
 description TO_MDF1_FW
 switchport trunk allowed vlan 10,20,30,40,150,250
 switchport mode trunk
 no shutdown
exit
!
!=====================================================
! Finance Firewall Connections
!=====================================================
interface TenGigabitEthernet1/0/3
 description TO_MDF2_FNFW
 switchport trunk allowed vlan 150,250
 switchport mode trunk
 no shutdown
exit
!
interface TenGigabitEthernet1/0/4
 description TO_MDF1_FNFW
 switchport trunk allowed vlan 150,250
 switchport mode trunk
 no shutdown
exit
!
!=====================================================
! Core-to-Core Port Channel Configuration
!=====================================================
interface port-channel5
 description TO_MDF1_CORE
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan all
 no shutdown
exit
!
interface range TenGigabitEthernet1/0/5-8
 description TO_MDF1_CORE
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan all
 channel-group 5 mode active
 no shutdown
exit
!
!=====================================================
! IDF Access Switch Port Channel Configuration
!=====================================================
!
! IDF1 Port Channel
interface port-channel9
 description TO_IDF1_ACC
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40,250
 no shutdown
exit
!
interface range TenGigabitEthernet1/0/9-10
 description TO_IDF1_ACC
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40,250
 channel-group 9 mode active
 no shutdown
exit
!
! IDF3 Port Channel
interface port-channel13
 description TO_IDF3_ACC
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40,250
 no shutdown
exit
!
interface range TenGigabitEthernet1/0/13-14
 description TO_IDF3_ACC
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40,250
 channel-group 13 mode active
 no shutdown
exit
!
! IDF4 Port Channel
interface port-channel15
 description TO_IDF4_ACC
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40,250
 no shutdown
exit
!
interface range TenGigabitEthernet1/0/15-16
 description TO_IDF4_ACC
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40,250
 channel-group 15 mode active
 no shutdown
exit
!
!=====================================================
! Network Services Connections
!=====================================================
!
! DHCP Server Port Channel
interface port-channel21
 description TO_DHCP_SERVER
 switchport mode access
 switchport access vlan 200
 spanning-tree portfast
 no shutdown
exit
!
interface range GigabitEthernet1/0/1-2
 description TO_DHCP_SERVER
 switchport mode access
 switchport access vlan 200
 spanning-tree portfast
 channel-group 21 mode active
 no shutdown
exit
!
! DNS Server Port Channel
interface port-channel22
 description TO_DNS_SERVER
 switchport mode access
 switchport access vlan 200
 spanning-tree portfast
 no shutdown
exit
!
interface range GigabitEthernet1/0/3-4
 description TO_DNS_SERVER
 switchport mode access
 switchport access vlan 200
 spanning-tree portfast
 channel-group 22 mode active
 no shutdown
exit
!
! Voice Gateway Port Channel
interface port-channel23
 description TO_VG_SERVER
 switchport mode access
 switchport access vlan 200
 spanning-tree portfast
 no shutdown
exit
!
interface range GigabitEthernet1/0/5-6
 description TO_VG_SERVER
 switchport mode access
 switchport access vlan 200
 spanning-tree portfast
 channel-group 23 mode active
 no shutdown
exit
!
! WLC Port Channel
interface port-channel24
 description TO_WLC
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 30,40,250
 no shutdown
exit
!
interface range GigabitEthernet1/0/7-8
 description TO_WLC
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 30,40,250
 channel-group 24 mode active
 no shutdown
exit
!
! FSP Server
interface GigabitEthernet1/0/4
 description TO_FSP_SERVER
 switchport mode access
 switchport access vlan 200
 spanning-tree portfast
 no shutdown
exit
!
!=====================================================
! Management Network Connection
!=====================================================
interface GigabitEthernet1/0/24
 description TO_MGMT_NET
 switchport mode access
 switchport access vlan 250
 spanning-tree portfast
 spanning-tree bpduguard enable
 no shutdown
exit
!
!=====================================================
! Spanning Tree Configuration
!=====================================================
spanning-tree mode rapid-pvst
spanning-tree extend system-id
!
! Set as root bridge for secondary VLANs
spanning-tree vlan 20,40,110,250 priority 4096
! Set as secondary root bridge for primary VLANs
spanning-tree vlan 10,30,100,200 priority 8192
!
ntp server 10.0.200.22
!
end
write memory
!=====================================================
! End of Configuration
!=====================================================
```