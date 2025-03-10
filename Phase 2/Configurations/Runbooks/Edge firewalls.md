
# SeaIce Primary Edgefirewall

```
!=====================================================
!
!  _____ _     _            
! |  ___(_)_ _| |__ ___ _ __
! | |_  | | '__| / _/ _ \ '_ \
! |  _| | | |  | \__\  __/ | | |
! |_|   |_|_|  |_\__/\___|_| |_|
!                              
!=====================================================
!
! Palo Alto Edge Firewall (Primary)
!
!=====================================================
! System Configuration
!=====================================================

# Configure hostname and domain
set deviceconfig system hostname SIC-MDF1-FW-01
set deviceconfig system domain-name seaice.local

# Authentication profile and administrator account
set shared authentication-profile local-admin authentication-method password-only
set mgt-config users admin phash $1$pnrbmvpe$0Zi5nxZbXk5sKsRiULan7.
set mgt-config users admin permissions role-based superuser yes

# NTP and DNS configuration
set deviceconfig system ntp-servers primary-ntp-server server 10.0.200.21 
set deviceconfig system dns-setting servers primary 10.0.200.102
set deviceconfig system dns-setting servers secondary 10.0.200.102

# Set timezone and log settings
set deviceconfig system timezone US/Pacific
set deviceconfig system log-settings system max-size 500
set deviceconfig system log-settings config max-size 500

# Allow management access
set deviceconfig system permitted-ip 10.0.250.0/26

!=====================================================
! Network Interface Configuration
!=====================================================

# WAN Interface (Century Link)
set network interface ethernet ethernet1/1 layer3 ipv4 ndp-proxy disabled
set network interface ethernet ethernet1/1 layer3 ip 152.162.97.182/30
set network interface ethernet ethernet1/1 layer3 mtu 1500
set network interface ethernet ethernet1/1 comment "WAN_CENTURY_LINK"
set network interface ethernet ethernet1/1 link-speed auto
set network interface ethernet ethernet1/1 link-duplex auto
set network interface ethernet ethernet1/1 link-state auto

# Core Switch Connections
set network interface ethernet ethernet1/2 layer3 ipv4 ndp-proxy disabled
set network interface ethernet ethernet1/2 layer3 ip 10.0.150.5/29
set network interface ethernet ethernet1/2 layer3 mtu 1500
set network interface ethernet ethernet1/2 comment "TO_MDF1_CORE"
set network interface ethernet ethernet1/2 link-speed auto
set network interface ethernet ethernet1/2 link-duplex auto
set network interface ethernet ethernet1/2 link-state auto

set network interface ethernet ethernet1/3 layer3 ipv4 ndp-proxy disabled
set network interface ethernet ethernet1/3 layer3 ip 10.0.150.7/29
set network interface ethernet ethernet1/3 layer3 mtu 1500
set network interface ethernet ethernet1/3 comment "TO_MDF2_CORE"
set network interface ethernet ethernet1/3 link-speed auto
set network interface ethernet ethernet1/3 link-duplex auto
set network interface ethernet ethernet1/3 link-state auto

# HA Interfaces
set network interface ethernet ethernet1/4 layer3 ipv4 ndp-proxy disabled
set network interface ethernet ethernet1/4 comment "HA1_LINK"
set network interface ethernet ethernet1/4 link-speed auto
set network interface ethernet ethernet1/4 link-duplex auto
set network interface ethernet ethernet1/4 link-state auto

set network interface ethernet ethernet1/5 layer3 ipv4 ndp-proxy disabled
set network interface ethernet ethernet1/5 comment "HA2_LINK"
set network interface ethernet ethernet1/5 link-speed auto
set network interface ethernet ethernet1/5 link-duplex auto
set network interface ethernet ethernet1/5 link-state auto

# Management Interface
set network interface ethernet ethernet1/6 layer3 ipv4 ndp-proxy disabled
set network interface ethernet ethernet1/6 layer3 ip 10.0.250.11/26
set network interface ethernet ethernet1/6 layer3 mtu 1500
set network interface ethernet ethernet1/6 comment "MGT"
set network interface ethernet ethernet1/6 link-speed auto
set network interface ethernet ethernet1/6 link-duplex auto
set network interface ethernet ethernet1/6 link-state auto

# Loopback Interface
set network interface loopback units loopback.1 ip 10.0.250.101/32
set network interface loopback units loopback.1 comment "MGMT_LOOPBACK"

!=====================================================
! Security Zone Configuration
!=====================================================

# Define Security Zones
set zone external network layer3 ethernet1/1
set zone internal network layer3 ethernet1/2
set zone internal network layer3 ethernet1/3
set zone management network layer3 ethernet1/6

# Zone Protection Profiles
set zone external network zone-protection-profile Zone-Protection-External
set zone internal network zone-protection-profile Zone-Protection-Internal
set zone management network zone-protection-profile Zone-Protection-Management

!=====================================================
! Virtual Router & Routing Configuration
!=====================================================

# Virtual Router
set network virtual-router default interface ethernet1/1
set network virtual-router default interface ethernet1/2
set network virtual-router default interface ethernet1/3
set network virtual-router default interface loopback.1

# Static Routes
set network virtual-router default routing-table ip static-route DEFAULT-ROUTE interface ethernet1/1 destination 0.0.0.0/0 nexthop ip-address 152.162.97.181

# OSPF Configuration
set network virtual-router default protocol ospf enable yes
set network virtual-router default protocol ospf router-id 10.0.250.101
set network virtual-router default protocol ospf area 0.0.0.0 interface ethernet1/2 enable yes
set network virtual-router default protocol ospf area 0.0.0.0 interface ethernet1/2 passive no
set network virtual-router default protocol ospf area 0.0.0.0 interface ethernet1/3 enable yes
set network virtual-router default protocol ospf area 0.0.0.0 interface ethernet1/3 passive no
set network virtual-router default protocol ospf area 0.0.0.0 interface loopback.1 enable yes
set network virtual-router default protocol ospf area 0.0.0.0 interface loopback.1 passive yes
set network virtual-router default protocol ospf enable yes
set network virtual-router default protocol ospf reject-default-route yes
set network virtual-router default protocol ospf redistribute bgp enable yes

# BGP Configuration
set network virtual-router default protocol bgp enable yes
set network virtual-router default protocol bgp router-id 10.0.250.101
set network virtual-router default protocol bgp local-as 6214
set network virtual-router default protocol bgp peer-group Century peer 152.162.97.181 connection-options keep-alive-interval 30
set network virtual-router default protocol bgp peer-group Century peer 152.162.97.181 connection-options hold-time 90
set network virtual-router default protocol bgp peer-group Century peer 152.162.97.181 connection-options min-route-adv-interval 30
set network virtual-router default protocol bgp peer-group Century peer 152.162.97.181 peer-as 65510
set network virtual-router default protocol bgp peer-group Century peer 152.162.97.181 enable yes
set network virtual-router default protocol bgp peer-group Century peer 152.162.97.181 local-address 152.162.97.182
set network virtual-router default protocol bgp peer-group Century peer 152.162.97.181 auth profile BGP-AUTH
set network virtual-router default protocol bgp peer-group Century peer 152.162.97.181 auth key 09617E2A5423051D05180D2F39
set network virtual-router default protocol bgp peer-group Century peer 152.162.97.181 connection-options incoming-bgp-connection yes
set network virtual-router default protocol bgp peer-group Century peer 152.162.97.181 connection-options outgoing-bgp-connection yes
set network virtual-router default protocol bgp peer-group Century peer 152.162.97.181 max-prefixes 1000
set network virtual-router default protocol bgp peer-group Century aggregated-confed-as-path yes
set network virtual-router default protocol bgp policy export-rule NO-ADVERTISE used-by peer-group Century
set network virtual-router default protocol bgp policy import-rule ACCEPT-DEFAULT used-by peer-group Century

# Route Redistribution
set network virtual-router default protocol redist-profile DEFAULT_ONLY filter type static destination 0.0.0.0/0
set network virtual-router default protocol bgp redist-rules static enable yes
set network virtual-router default protocol bgp redist-rules static address-family-identifier ipv4
set network virtual-router default protocol bgp redist-rules static set-origin igp
set network virtual-router default protocol bgp redist-rules static redist-profile DEFAULT_ONLY

!=====================================================
! NAT Configuration
!=====================================================

# Source NAT Rules
set rulebase nat rules OUTBOUND_NAT_USERS source zone internal
set rulebase nat rules OUTBOUND_NAT_USERS source address 10.0.10.0/23
set rulebase nat rules OUTBOUND_NAT_USERS destination zone external
set rulebase nat rules OUTBOUND_NAT_USERS destination address any
set rulebase nat rules OUTBOUND_NAT_USERS service any
set rulebase nat rules OUTBOUND_NAT_USERS to interface ethernet1/1
set rulebase nat rules OUTBOUND_NAT_USERS source-translation dynamic-ip-and-port interface-address interface ethernet1/1

set rulebase nat rules OUTBOUND_NAT_VOICE source zone internal
set rulebase nat rules OUTBOUND_NAT_VOICE source address 10.0.20.0/23
set rulebase nat rules OUTBOUND_NAT_VOICE destination zone external
set rulebase nat rules OUTBOUND_NAT_VOICE destination address any
set rulebase nat rules OUTBOUND_NAT_VOICE service any
set rulebase nat rules OUTBOUND_NAT_VOICE to interface ethernet1/1
set rulebase nat rules OUTBOUND_NAT_VOICE source-translation dynamic-ip-and-port interface-address interface ethernet1/1

set rulebase nat rules OUTBOUND_NAT_WIRELESS source zone internal
set rulebase nat rules OUTBOUND_NAT_WIRELESS source address 10.0.30.0/22
set rulebase nat rules OUTBOUND_NAT_WIRELESS destination zone external
set rulebase nat rules OUTBOUND_NAT_WIRELESS destination address any
set rulebase nat rules OUTBOUND_NAT_WIRELESS service any
set rulebase nat rules OUTBOUND_NAT_WIRELESS to interface ethernet1/1
set rulebase nat rules OUTBOUND_NAT_WIRELESS source-translation dynamic-ip-and-port interface-address interface ethernet1/1

set rulebase nat rules OUTBOUND_NAT_GUEST source zone internal
set rulebase nat rules OUTBOUND_NAT_GUEST source address 10.0.40.0/22
set rulebase nat rules OUTBOUND_NAT_GUEST destination zone external
set rulebase nat rules OUTBOUND_NAT_GUEST destination address any
set rulebase nat rules OUTBOUND_NAT_GUEST service any
set rulebase nat rules OUTBOUND_NAT_GUEST to interface ethernet1/1
set rulebase nat rules OUTBOUND_NAT_GUEST source-translation dynamic-ip-and-port interface-address interface ethernet1/1

set rulebase nat rules OUTBOUND_NAT_SERVICES source zone internal
set rulebase nat rules OUTBOUND_NAT_SERVICES source address 10.0.200.0/26
set rulebase nat rules OUTBOUND_NAT_SERVICES destination zone external
set rulebase nat rules OUTBOUND_NAT_SERVICES destination address any
set rulebase nat rules OUTBOUND_NAT_SERVICES service any
set rulebase nat rules OUTBOUND_NAT_SERVICES to interface ethernet1/1
set rulebase nat rules OUTBOUND_NAT_SERVICES source-translation dynamic-ip-and-port interface-address interface ethernet1/1

# Destination NAT for Inbound Services
set rulebase nat rules DMZ_WEB destination zone external
set rulebase nat rules DMZ_WEB destination address 152.162.97.182
set rulebase nat rules DMZ_WEB service TCP_443
set rulebase nat rules DMZ_WEB to interface ethernet1/1
set rulebase nat rules DMZ_WEB destination-translation translated-address 10.0.200.21
set rulebase nat rules DMZ_WEB destination-translation translated-port 443

set rulebase nat rules VPN_ACCESS destination zone external
set rulebase nat rules VPN_ACCESS destination address 152.162.97.182
set rulebase nat rules VPN_ACCESS service UDP_4500_500
set rulebase nat rules VPN_ACCESS to interface ethernet1/1
set rulebase nat rules VPN_ACCESS destination-translation translated-address 10.0.200.31

!=====================================================
! Security Policies
!=====================================================

# Inbound Rules
set rulebase security rules ALLOW_WEB_INBOUND from external
set rulebase security rules ALLOW_WEB_INBOUND to internal
set rulebase security rules ALLOW_WEB_INBOUND source any
set rulebase security rules ALLOW_WEB_INBOUND destination 10.0.200.21
set rulebase security rules ALLOW_WEB_INBOUND application web-browsing
set rulebase security rules ALLOW_WEB_INBOUND service TCP_443
set rulebase security rules ALLOW_WEB_INBOUND action allow
set rulebase security rules ALLOW_WEB_INBOUND log-start yes
set rulebase security rules ALLOW_WEB_INBOUND log-end yes

set rulebase security rules ALLOW_VPN_INBOUND from external
set rulebase security rules ALLOW_VPN_INBOUND to internal
set rulebase security rules ALLOW_VPN_INBOUND source any
set rulebase security rules ALLOW_VPN_INBOUND destination 10.0.200.31
set rulebase security rules ALLOW_VPN_INBOUND application ike ssl-vpn
set rulebase security rules ALLOW_VPN_INBOUND service UDP_4500_500
set rulebase security rules ALLOW_VPN_INBOUND action allow
set rulebase security rules ALLOW_VPN_INBOUND log-start yes
set rulebase security rules ALLOW_VPN_INBOUND log-end yes

# Outbound Rules
set rulebase security rules ALLOW_OUTBOUND from internal
set rulebase security rules ALLOW_OUTBOUND to external
set rulebase security rules ALLOW_OUTBOUND source any
set rulebase security rules ALLOW_OUTBOUND destination any
set rulebase security rules ALLOW_OUTBOUND application any
set rulebase security rules ALLOW_OUTBOUND service any
set rulebase security rules ALLOW_OUTBOUND action allow
set rulebase security rules ALLOW_OUTBOUND log-start no
set rulebase security rules ALLOW_OUTBOUND log-end yes

# Block Rule (Default Deny)
set rulebase security rules BLOCK_ALL from any
set rulebase security rules BLOCK_ALL to any
set rulebase security rules BLOCK_ALL source any
set rulebase security rules BLOCK_ALL destination any
set rulebase security rules BLOCK_ALL application any
set rulebase security rules BLOCK_ALL service any
set rulebase security rules BLOCK_ALL action deny
set rulebase security rules BLOCK_ALL log-start yes
set rulebase security rules BLOCK_ALL log-end yes

!=====================================================
! High Availability Configuration
!=====================================================

# HA Configuration
set high-availability interface ha1 port ethernet1/4
set high-availability interface ha2 port ethernet1/5
set high-availability group 1 mode active-passive
set high-availability group 1 configuration-synchronization enabled yes
set high-availability group 1 peer-ip 10.0.250.12
set high-availability group 1 election-option priority primary
set high-availability group 1 preemptive yes
set high-availability group 1 state-synchronization enabled yes

# Floating IP Addresses
set high-availability group 1 virtual-address ethernet1/2 ip 10.0.150.4/29
set high-availability group 1 virtual-address ethernet1/3 ip 10.0.150.4/29

!=====================================================
! Monitoring and Logging
!=====================================================

# Syslog server
set shared log-settings syslog SIC-Syslog server Primary-Syslog server 10.0.250.71
set shared log-settings syslog SIC-Syslog server Primary-Syslog transport UDP
set shared log-settings syslog SIC-Syslog server Primary-Syslog port 514
set shared log-settings syslog SIC-Syslog server Primary-Syslog format BSD
set shared log-settings syslog SIC-Syslog server Primary-Syslog facility LOG_USER

# Log Forwarding Profile
set shared log-settings profiles SIC-Log-Profile match-list System_Logs log-type system
set shared log-settings profiles SIC-Log-Profile match-list System_Logs filter "( severity eq critical ) or ( severity eq high ) or ( severity eq medium )"
set shared log-settings profiles SIC-Log-Profile match-list System_Logs send-to syslog SIC-Syslog

set shared log-settings profiles SIC-Log-Profile match-list Traffic_Logs log-type traffic
set shared log-settings profiles SIC-Log-Profile match-list Traffic_Logs filter "( severity eq critical ) or ( severity eq high )"
set shared log-settings profiles SIC-Log-Profile match-list Traffic_Logs send-to syslog SIC-Syslog

set shared log-settings profiles SIC-Log-Profile match-list Threat_Logs log-type threat
set shared log-settings profiles SIC-Log-Profile match-list Threat_Logs filter "( severity eq critical ) or ( severity eq high ) or ( severity eq medium ) or ( severity eq low )"
set shared log-settings profiles SIC-Log-Profile match-list Threat_Logs send-to syslog SIC-Syslog

# SNMP Configuration
set deviceconfig system snmp-setting access-setting version v2c
set deviceconfig system snmp-setting access-setting trap v2c SIC-SNMP-Community
set deviceconfig system snmp-setting access-setting trap snmpv3 profile
set deviceconfig system snmp-setting system name "SIC-MDF1-FW-01"
set deviceconfig system snmp-setting system location "MDF1, SeaIce Creamery HQ"
set deviceconfig system snmp-setting system contact "admin@seaice.local"
set deviceconfig system snmp-setting snmp-manager 10.0.250.71
```

# SeaIce Secondary Edge firewall

```
!=====================================================
!
!  _____ _     _            
! |  ___(_)_ _| |__ ___ _ __
! | |_  | | '__| / _/ _ \ '_ \
! |  _| | | |  | \__\  __/ | | |
! |_|   |_|_|  |_\__/\___|_| |_|
!                              
!=====================================================
!
! Palo Alto Edge Firewall (Secondary)
!
!=====================================================
! System Configuration
!=====================================================

# Configure hostname and domain
set deviceconfig system hostname SIC-MDF2-FW-01
set deviceconfig system domain-name seaice.local

# Authentication profile and administrator account
set shared authentication-profile local-admin authentication-method password-only
set mgt-config users admin phash $1$pnrbmvpe$0Zi5nxZbXk5sKsRiULan7.
set mgt-config users admin permissions role-based superuser yes

# NTP and DNS configuration
set deviceconfig system ntp-servers primary-ntp-server server 10.0.200.22
set deviceconfig system dns-setting servers primary 10.0.200.102
set deviceconfig system dns-setting servers secondary 10.0.200.102

# Set timezone and log settings
set deviceconfig system timezone US/Pacific
set deviceconfig system log-settings system max-size 500
set deviceconfig system log-settings config max-size 500

# Allow management access
set deviceconfig system permitted-ip 10.0.250.0/26

!=====================================================
! Network Interface Configuration
!=====================================================

# WAN Interface (Comcast)
set network interface ethernet ethernet1/1 layer3 ipv4 ndp-proxy disabled
set network interface ethernet ethernet1/1 layer3 ip 68.136.143.66/30
set network interface ethernet ethernet1/1 layer3 mtu 1500
set network interface ethernet ethernet1/1 comment "WAN_COMCAST"
set network interface ethernet ethernet1/1 link-speed auto
set network interface ethernet ethernet1/1 link-duplex auto
set network interface ethernet ethernet1/1 link-state auto

# Core Switch Connections
set network interface ethernet ethernet1/2 layer3 ipv4 ndp-proxy disabled
set network interface ethernet ethernet1/2 layer3 ip 10.0.150.6/29
set network interface ethernet ethernet1/2 layer3 mtu 1500
set network interface ethernet ethernet1/2 comment "TO_MDF2_CORE"
set network interface ethernet ethernet1/2 link-speed auto
set network interface ethernet ethernet1/2 link-duplex auto
set network interface ethernet ethernet1/2 link-state auto

set network interface ethernet ethernet1/3 layer3 ipv4 ndp-proxy disabled
set network interface ethernet ethernet1/3 layer3 ip 10.0.150.8/29
set network interface ethernet ethernet1/3 layer3 mtu 1500
set network interface ethernet ethernet1/3 comment "TO_MDF1_CORE"
set network interface ethernet ethernet1/3 link-speed auto
set network interface ethernet ethernet1/3 link-duplex auto
set network interface ethernet ethernet1/3 link-state auto

# HA Interfaces
set network interface ethernet ethernet1/4 layer3 ipv4 ndp-proxy disabled
set network interface ethernet ethernet1/4 comment "HA1_LINK"
set network interface ethernet ethernet1/4 link-speed auto
set network interface ethernet ethernet1/4 link-duplex auto
set network interface ethernet ethernet1/4 link-state auto

set network interface ethernet ethernet1/5 layer3 ipv4 ndp-proxy disabled
set network interface ethernet ethernet1/5 comment "HA2_LINK"
set network interface ethernet ethernet1/5 link-speed auto
set network interface ethernet ethernet1/5 link-duplex auto
set network interface ethernet ethernet1/5 link-state auto

# Management Interface
set network interface ethernet ethernet1/6 layer3 ipv4 ndp-proxy disabled
set network interface ethernet ethernet1/6 layer3 ip 10.0.250.12/26
set network interface ethernet ethernet1/6 layer3 mtu 1500
set network interface ethernet ethernet1/6 comment "MGT"
set network interface ethernet ethernet1/6 link-speed auto
set network interface ethernet ethernet1/6 link-duplex auto
set network interface ethernet ethernet1/6 link-state auto

# Loopback Interface
set network interface loopback units loopback.1 ip 10.0.250.102/32
set network interface loopback units loopback.1 comment "MGMT_LOOPBACK"

!=====================================================
! Security Zone Configuration
!=====================================================

# Define Security Zones
set zone external network layer3 ethernet1/1
set zone internal network layer3 ethernet1/2
set zone internal network layer3 ethernet1/3
set zone management network layer3 ethernet1/6

# Zone Protection Profiles
set zone external network zone-protection-profile Zone-Protection-External
set zone internal network zone-protection-profile Zone-Protection-Internal
set zone management network zone-protection-profile Zone-Protection-Management

!=====================================================
! Virtual Router & Routing Configuration
!=====================================================

# Virtual Router
set network virtual-router default interface ethernet1/1
set network virtual-router default interface ethernet1/2
set network virtual-router default interface ethernet1/3
set network virtual-router default interface loopback.1

# Static Routes
set network virtual-router default routing-table ip static-route DEFAULT-ROUTE interface ethernet1/1 destination 0.0.0.0/0 nexthop ip-address 68.136.143.65

# OSPF Configuration
set network virtual-router default protocol ospf enable yes
set network virtual-router default protocol ospf router-id 10.0.250.102
set network virtual-router default protocol ospf area 0.0.0.0 interface ethernet1/2 enable yes
set network virtual-router default protocol ospf area 0.0.0.0 interface ethernet1/2 passive no
set network virtual-router default protocol ospf area 0.0.0.0 interface ethernet1/3 enable yes
set network virtual-router default protocol ospf area 0.0.0.0 interface ethernet1/3 passive no
set network virtual-router default protocol ospf area 0.0.0.0 interface loopback.1 enable yes
set network virtual-router default protocol ospf area 0.0.0.0 interface loopback.1 passive yes
set network virtual-router default protocol ospf enable yes
set network virtual-router default protocol ospf reject-default-route yes
set network virtual-router default protocol ospf redistribute bgp enable yes

# BGP Configuration
set network virtual-router default protocol bgp enable yes
set network virtual-router default protocol bgp router-id 10.0.250.102
set network virtual-router default protocol bgp local-as 6211
set network virtual-router default protocol bgp peer-group Comcast peer 68.136.143.65 connection-options keep-alive-interval 30
set network virtual-router default protocol bgp peer-group Comcast peer 68.136.143.65 connection-options hold-time 90
set network virtual-router default protocol bgp peer-group Comcast peer 68.136.143.65 connection-options min-route-adv-interval 30
set network virtual-router default protocol bgp peer-group Comcast peer 68.136.143.65 peer-as 65000
set network virtual-router default protocol bgp peer-group Comcast peer 68.136.143.65 enable yes
set network virtual-router default protocol bgp peer-group Comcast peer 68.136.143.65 local-address 68.136.143.66
set network virtual-router default protocol bgp peer-group Comcast peer 68.136.143.65 auth profile BGP-AUTH
set network virtual-router default protocol bgp peer-group Comcast peer 68.136.143.65 auth key 0961612A542702010202013938
set network virtual-router default protocol bgp peer-group Comcast peer 68.136.143.65 connection-options incoming-bgp-connection yes
set network virtual-router default protocol bgp peer-group Comcast peer 68.136.143.65 connection-options outgoing-bgp-connection yes
set network virtual-router default protocol bgp peer-group Comcast peer 68.136.143.65 max-prefixes 1000
set network virtual-router default protocol bgp peer-group Comcast aggregated-confed-as-path yes
set network virtual-router default protocol bgp policy export-rule NO-ADVERTISE used-by peer-group Comcast
set network virtual-router default protocol bgp policy import-rule ACCEPT-DEFAULT used-by peer-group Comcast

# Route Redistribution
set network virtual-router default protocol redist-profile DEFAULT_ONLY filter type static destination 0.0.0.0/0
set network virtual-router default protocol bgp redist-rules static enable yes
set network virtual-router default protocol bgp redist-rules static address-family-identifier ipv4
set network virtual-router default protocol bgp redist-rules static set-origin igp
set network virtual-router default protocol bgp redist-rules static redist-profile DEFAULT_ONLY

!=====================================================
! NAT Configuration
!=====================================================

# Source NAT Rules
set rulebase nat rules OUTBOUND_NAT_USERS source zone internal
set rulebase nat rules OUTBOUND_NAT_USERS source address 10.0.10.0/23
set rulebase nat rules OUTBOUND_NAT_USERS destination zone external
set rulebase nat rules OUTBOUND_NAT_USERS destination address any
set rulebase nat rules OUTBOUND_NAT_USERS service any
set rulebase nat rules OUTBOUND_NAT_USERS to interface ethernet1/1
set rulebase nat rules OUTBOUND_NAT_USERS source-translation dynamic-ip-and-port interface-address interface ethernet1/1

set rulebase nat rules OUTBOUND_NAT_VOICE source zone internal
set rulebase nat rules OUTBOUND_NAT_VOICE source address 10.0.20.0/23
set rulebase nat rules OUTBOUND_NAT_VOICE destination zone external
set rulebase nat rules OUTBOUND_NAT_VOICE destination address any
set rulebase nat rules OUTBOUND_NAT_VOICE service any
set rulebase nat rules OUTBOUND_NAT_VOICE to interface ethernet1/1
set rulebase nat rules OUTBOUND_NAT_VOICE source-translation dynamic-ip-and-port interface-address interface ethernet1/1

set rulebase nat rules OUTBOUND_NAT_WIRELESS source zone internal
set rulebase nat rules OUTBOUND_NAT_WIRELESS source address 10.0.30.0/22
set rulebase nat rules OUTBOUND_NAT_WIRELESS destination zone external
set rulebase nat rules OUTBOUND_NAT_WIRELESS destination address any
set rulebase nat rules OUTBOUND_NAT_WIRELESS service any
set rulebase nat rules OUTBOUND_NAT_WIRELESS to interface ethernet1/1
set rulebase nat rules OUTBOUND_NAT_WIRELESS source-translation dynamic-ip-and-port interface-address interface ethernet1/1

set rulebase nat rules OUTBOUND_NAT_GUEST source zone internal
set rulebase nat rules OUTBOUND_NAT_GUEST source address 10.0.40.0/22
set rulebase nat rules OUTBOUND_NAT_GUEST destination zone external
set rulebase nat rules OUTBOUND_NAT_GUEST destination address any
set rulebase nat rules OUTBOUND_NAT_GUEST service any
set rulebase nat rules OUTBOUND_NAT_GUEST to interface ethernet1/1
set rulebase nat rules OUTBOUND_NAT_GUEST source-translation dynamic-ip-and-port interface-address interface ethernet1/1

set rulebase nat rules OUTBOUND_NAT_SERVICES source zone internal
set rulebase nat rules OUTBOUND_NAT_SERVICES source address 10.0.200.0/26
set rulebase nat rules OUTBOUND_NAT_SERVICES destination zone external
set rulebase nat rules OUTBOUND_NAT_SERVICES destination address any
set rulebase nat rules OUTBOUND_NAT_SERVICES service any
set rulebase nat rules OUTBOUND_NAT_SERVICES to interface ethernet1/1
set rulebase nat rules OUTBOUND_NAT_SERVICES source-translation dynamic-ip-and-port interface-address interface ethernet1/1

# Destination NAT for Inbound Services
set rulebase nat rules DMZ_WEB_BACKUP destination zone external
set rulebase nat rules DMZ_WEB_BACKUP destination address 68.136.143.66
set rulebase nat rules DMZ_WEB_BACKUP service TCP_443
set rulebase nat rules DMZ_WEB_BACKUP to interface ethernet1/1
set rulebase nat rules DMZ_WEB_BACKUP destination-translation translated-address 10.0.200.21
set rulebase nat rules DMZ_WEB_BACKUP destination-translation translated-port 443

set rulebase nat rules VPN_ACCESS_BACKUP destination zone external
set rulebase nat rules VPN_ACCESS_BACKUP destination address 68.136.143.66
set rulebase nat rules VPN_ACCESS_BACKUP service UDP_4500_500
set rulebase nat rules VPN_ACCESS_BACKUP to interface ethernet1/1
set rulebase nat rules VPN_ACCESS_BACKUP destination-translation translated-address 10.0.200.31

!=====================================================
! Security Policies
!=====================================================

# Inbound Rules
set rulebase security rules ALLOW_WEB_INBOUND from external
set rulebase security rules ALLOW_WEB_INBOUND to internal
set rulebase security rules ALLOW_WEB_INBOUND source any
set rulebase security rules ALLOW_WEB_INBOUND destination 10.0.200.21
set rulebase security rules ALLOW_WEB_INBOUND application web-browsing
set rulebase security rules ALLOW_WEB_INBOUND service TCP_443
set rulebase security rules ALLOW_WEB_INBOUND action allow
set rulebase security rules ALLOW_WEB_INBOUND log-start yes
set rulebase security rules ALLOW_WEB_INBOUND log-end yes

set rulebase security rules ALLOW_VPN_INBOUND from external
set rulebase security rules ALLOW_VPN_INBOUND to internal
set rulebase security rules ALLOW_VPN_INBOUND source any
set rulebase security rules ALLOW_VPN_INBOUND destination 10.0.200.31
set rulebase security rules ALLOW_VPN_INBOUND application ike ssl-vpn
set rulebase security rules ALLOW_VPN_INBOUND service UDP_4500_500
set rulebase security rules ALLOW_VPN_INBOUND action allow
set rulebase security rules ALLOW_VPN_INBOUND log-start yes
set rulebase security rules ALLOW_VPN_INBOUND log-end yes

# Outbound Rules
set rulebase security rules ALLOW_OUTBOUND from internal
set rulebase security rules ALLOW_OUTBOUND to external
set rulebase security rules ALLOW_OUTBOUND source any
set rulebase security rules ALLOW_OUTBOUND destination any
set rulebase security rules ALLOW_OUTBOUND application any
set rulebase security rules ALLOW_OUTBOUND service any
set rulebase security rules ALLOW_OUTBOUND action allow
set rulebase security rules ALLOW_OUTBOUND log-start no
set rulebase security rules ALLOW_OUTBOUND log-end yes

# Block Rule (Default Deny)
set rulebase security rules BLOCK_ALL from any
set rulebase security rules BLOCK_ALL to any
set rulebase security rules BLOCK_ALL source any
set rulebase security rules BLOCK_ALL destination any
set rulebase security rules BLOCK_ALL application any
set rulebase security rules BLOCK_ALL service any
set rulebase security rules BLOCK_ALL action deny
set rulebase security rules BLOCK_ALL log-start yes
set rulebase security rules BLOCK_ALL log-end yes

!=====================================================
! High Availability Configuration
!=====================================================

# HA Configuration
set high-availability interface ha1 port ethernet1/4
set high-availability interface ha2 port ethernet1/5
set high-availability group 1 mode active-passive
set high-availability group 1 configuration-synchronization enabled yes
set high-availability group 1 peer-ip 10.0.250.11
set high-availability group 1 election-option priority secondary
set high-availability group 1 preemptive yes
set high-availability group 1 state-synchronization enabled yes

# Floating IP Addresses
set high-availability group 1 virtual-address ethernet1/2 ip 10.0.150.4/29
set high-availability group 1 virtual-address ethernet1/3 ip 10.0.150.4/29

!=====================================================
! Monitoring and Logging
!=====================================================

# Syslog server
set shared log-settings syslog SIC-Syslog server Primary-Syslog server 10.0.250.72
set shared log-settings syslog SIC-Syslog server Primary-Syslog transport UDP
set shared log-settings syslog SIC-Syslog server Primary-Syslog port 514
set shared log-settings syslog SIC-Syslog server Primary-Syslog format BSD
set shared log-settings syslog SIC-Syslog server Primary-Syslog facility LOG_USER

# Log Forwarding Profile
set shared log-settings profiles SIC-Log-Profile match-list System_Logs log-type system
set shared log-settings profiles SIC-Log-Profile match-list System_Logs filter "( severity eq critical ) or ( severity eq high ) or ( severity eq medium )"
set shared log-settings profiles SIC-Log-Profile match-list System_Logs send-to syslog SIC-Syslog

set shared log-settings profiles SIC-Log-Profile match-list Traffic_Logs log-type traffic
set shared log-settings profiles SIC-Log-Profile match-list Traffic_Logs filter "( severity eq critical ) or ( severity eq high )"
set shared log-settings profiles SIC-Log-Profile match-list Traffic_Logs send-to syslog SIC-Syslog

set shared log-settings profiles SIC-Log-Profile match-list Threat_Logs log-type threat
set shared log-settings profiles SIC-Log-Profile match-list Threat_Logs filter "( severity eq critical ) or ( severity eq high ) or ( severity eq medium ) or ( severity eq low )"
set shared log-settings profiles SIC-Log-Profile match-list Threat_Logs send-to syslog SIC-Syslog

# SNMP Configuration
set deviceconfig system snmp-setting access-setting version v2c
set deviceconfig system snmp-setting access-setting trap v2c SIC-SNMP-Community
set deviceconfig system snmp-setting access-setting trap snmpv3 profile
set deviceconfig system snmp-setting system name "SIC-MDF2-FW-01"
set deviceconfig system snmp-setting system location "MDF2, SeaIce Creamery HQ"
set deviceconfig system snmp-setting system contact "admin@seaice.local"
set deviceconfig system snmp-setting snmp-manager 10.0.250.72
```