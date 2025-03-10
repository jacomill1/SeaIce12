# Primary Finance Firewall

```
!=====================================================
!
!  _____ _     _                         
! |  ___(_)_ _| |__ ___ _ __  ___ ___    
! | |_  | | '__| / _/ _ \ '_ \/ __/ _ \  
! |  _| | | |  | \__\  __/ | | \__\  __/ 
! |_|   |_|_|  |_\__/\___|_| |_|___/\___|
!                                         
!=====================================================
!
! Palo Alto Finance Firewall (Primary)
!
!=====================================================
! System Configuration
!=====================================================

# Configure hostname and domain
set deviceconfig system hostname SIC-MDF1-FNFW-01
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

# Finance Department Interfaces
set network interface ethernet ethernet1/1 layer3 ipv4 ndp-proxy disabled
set network interface ethernet ethernet1/1.100 layer3 ip 10.0.100.5/25
set network interface ethernet ethernet1/1.100 comment "FINANCE_DATA"
set network interface ethernet ethernet1/1.110 layer3 ip 10.0.110.5/25
set network interface ethernet ethernet1/1.110 comment "FINANCE_VOICE"

# Core Switch Connections
set network interface ethernet ethernet1/2 layer3 ipv4 ndp-proxy disabled
set network interface ethernet ethernet1/2 layer3 ip 10.0.150.10/29
set network interface ethernet ethernet1/2 layer3 mtu 1500
set network interface ethernet ethernet1/2 comment "TO_MDF1_CORE"
set network interface ethernet ethernet1/2 link-speed auto
set network interface ethernet ethernet1/2 link-duplex auto
set network interface ethernet ethernet1/2 link-state auto

set network interface ethernet ethernet1/3 layer3 ipv4 ndp-proxy disabled
set network interface ethernet ethernet1/3 layer3 ip 10.0.150.12/29
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
set network interface ethernet ethernet1/6 layer3 ip 10.0.250.21/26
set network interface ethernet ethernet1/6 layer3 mtu 1500
set network interface ethernet ethernet1/6 comment "MGT"
set network interface ethernet ethernet1/6 link-speed auto
set network interface ethernet ethernet1/6 link-duplex auto
set network interface ethernet ethernet1/6 link-state auto

# Loopback Interface
set network interface loopback units loopback.1 ip 10.0.250.103/32
set network interface loopback units loopback.1 comment "MGMT_LOOPBACK"

!=====================================================
! Security Zone Configuration
!=====================================================

# Define Security Zones
set zone finance network layer3 ethernet1/1.100
set zone finance network layer3 ethernet1/1.110
set zone transit network layer3 ethernet1/2
set zone transit network layer3 ethernet1/3
set zone management network layer3 ethernet1/6

# Zone Protection Profiles
set zone finance network zone-protection-profile Zone-Protection-Finance
set zone transit network zone-protection-profile Zone-Protection-Transit
set zone management network zone-protection-profile Zone-Protection-Management

!=====================================================
! Virtual Router & Routing Configuration
!=====================================================

# Virtual Router
set network virtual-router default interface ethernet1/1.100
set network virtual-router default interface ethernet1/1.110
set network virtual-router default interface ethernet1/2
set network virtual-router default interface ethernet1/3
set network virtual-router default interface loopback.1

# Static Routes
set network virtual-router default routing-table ip static-route TO_FSP_PROXY interface ethernet1/2 destination 10.0.200.0/26 nexthop ip-address 10.0.150.2
set network virtual-router default routing-table ip static-route TO_SERVICES interface ethernet1/2 destination 10.0.200.0/26 nexthop ip-address 10.0.150.2
set network virtual-router default routing-table ip static-route TO_INTERNET interface ethernet1/2 destination 0.0.0.0/0 nexthop ip-address 10.0.150.2

# OSPF Configuration
set network virtual-router default protocol ospf enable yes
set network virtual-router default protocol ospf router-id 10.0.250.103
set network virtual-router default protocol ospf area 0.0.0.0 interface ethernet1/2 enable yes
set network virtual-router default protocol ospf area 0.0.0.0 interface ethernet1/2 passive no
set network virtual-router default protocol ospf area 0.0.0.0 interface ethernet1/3 enable yes
set network virtual-router default protocol ospf area 0.0.0.0 interface ethernet1/3 passive no
set network virtual-router default protocol ospf area 0.0.0.0 interface loopback.1 enable yes
set network virtual-router default protocol ospf area 0.0.0.0 interface loopback.1 passive yes
set network virtual-router default protocol ospf enable yes
set network virtual-router default protocol ospf reject-default-route yes
set network virtual-router default protocol ospf redistribute connected enable yes

!=====================================================
! Security Policies
!=====================================================

# Allow Finance to FSP
set rulebase security rules FINANCE_TO_FSP from finance
set rulebase security rules FINANCE_TO_FSP to transit
set rulebase security rules FINANCE_TO_FSP source any
set rulebase security rules FINANCE_TO_FSP destination 10.0.200.104
set rulebase security rules FINANCE_TO_FSP application any
set rulebase security rules FINANCE_TO_FSP service any
set rulebase security rules FINANCE_TO_FSP action allow
set rulebase security rules FINANCE_TO_FSP log-start yes
set rulebase security rules FINANCE_TO_FSP log-end yes

# Allow Finance to DNS via FSP
set rulebase security rules FINANCE_TO_DNS from finance
set rulebase security rules FINANCE_TO_DNS to transit
set rulebase security rules FINANCE_TO_DNS source any
set rulebase security rules FINANCE_TO_DNS destination 10.0.200.102
set rulebase security rules FINANCE_TO_DNS application dns
set rulebase security rules FINANCE_TO_DNS service application-default
set rulebase security rules FINANCE_TO_DNS action allow
set rulebase security rules FINANCE_TO_DNS log-start no
set rulebase security rules FINANCE_TO_DNS log-end yes

# Allow Finance to DHCP
set rulebase security rules FINANCE_TO_DHCP from finance
set rulebase security rules FINANCE_TO_DHCP to transit
set rulebase security rules FINANCE_TO_DHCP source any
set rulebase security rules FINANCE_TO_DHCP destination 10.0.200.101
set rulebase security rules FINANCE_TO_DHCP application dhcp
set rulebase security rules FINANCE_TO_DHCP service application-default
set rulebase security rules FINANCE_TO_DHCP action allow
set rulebase security rules FINANCE_TO_DHCP log-start no
set rulebase security rules FINANCE_TO_DHCP log-end yes

# Allow Finance to Voice Gateway for VoIP
set rulebase security rules FINANCE_TO_VOICE from finance
set rulebase security rules FINANCE_TO_VOICE to transit
set rulebase security rules FINANCE_TO_VOICE source any
set rulebase security rules FINANCE_TO_VOICE destination 10.0.200.103
set rulebase security rules FINANCE_TO_VOICE application sip
set rulebase security rules FINANCE_TO_VOICE service application-default
set rulebase security rules FINANCE_TO_VOICE action allow
set rulebase security rules FINANCE_TO_VOICE log-start no
set rulebase security rules FINANCE_TO_VOICE log-end yes

# Block Finance to General Network
set rulebase security rules BLOCK_FINANCE_TO_GENERAL from finance
set rulebase security rules BLOCK_FINANCE_TO_GENERAL to transit
set rulebase security rules BLOCK_FINANCE_TO_GENERAL source any
set rulebase security rules BLOCK_FINANCE_TO_GENERAL destination 10.0.0.0/8
set rulebase security rules BLOCK_FINANCE_TO_GENERAL application any
set rulebase security rules BLOCK_FINANCE_TO_GENERAL service any
set rulebase security rules BLOCK_FINANCE_TO_GENERAL action deny
set rulebase security rules BLOCK_FINANCE_TO_GENERAL log-start yes
set rulebase security rules BLOCK_FINANCE_TO_GENERAL log-end yes

# Block Default Rule
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
set high-availability group 2 mode active-passive
set high-availability group 2 configuration-synchronization enabled yes
set high-availability group 2 peer-ip 10.0.250.22
set high-availability group 2 election-option priority primary
set high-availability group 2 preemptive yes
set high-availability group 2 state-synchronization enabled yes

# Floating IP Addresses
set high-availability group 2 virtual-address ethernet1/1.100 ip 10.0.100.1/25
set high-availability group 2 virtual-address ethernet1/1.110 ip 10.0.110.1/25
set high-availability group 2 virtual-address ethernet1/2 ip 10.0.150.9/29
set high-availability group 2 virtual-address ethernet1/3 ip 10.0.150.9/29

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
set deviceconfig system snmp-setting system name "SIC-MDF1-FNFW-01"
set deviceconfig system snmp-setting system location "MDF1, SeaIce Creamery HQ"
set deviceconfig system snmp-setting system contact "admin@seaice.local"
set deviceconfig system snmp-setting snmp-manager 10.0.250.71
```

## Secondary Finance Firewall (SIC-MDF2-FNFW-01) Configuration

```
!=====================================================
!
!  _____ _     _                         
! |  ___(_)_ _| |__ ___ _ __  ___ ___    
! | |_  | | '__| / _/ _ \ '_ \/ __/ _ \  
! |  _| | | |  | \__\  __/ | | \__\  __/ 
! |_|   |_|_|  |_\__/\___|_| |_|___/\___|
!                                         
!=====================================================
!
! Palo Alto Finance Firewall (Secondary)
!
!=====================================================
! System Configuration
!=====================================================

# Configure hostname and domain
set deviceconfig system hostname SIC-MDF2-FNFW-01
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

# Finance Department Interfaces
set network interface ethernet ethernet1/1 layer3 ipv4 ndp-proxy disabled
set network interface ethernet ethernet1/1.100 layer3 ip 10.0.100.6/25
set network interface ethernet ethernet1/1.100 comment "FINANCE_DATA"
set network interface ethernet ethernet1/1.110 layer3 ip 10.0.110.6/25
set network interface ethernet ethernet1/1.110 comment "FINANCE_VOICE"

# Core Switch Connections
set network interface ethernet ethernet1/2 layer3 ipv4 ndp-proxy disabled
set network interface ethernet ethernet1/2 layer3 ip 10.0.150.11/29
set network interface ethernet ethernet1/2 layer3 mtu 1500
set network interface ethernet ethernet1/2 comment "TO_MDF2_CORE"
set network interface ethernet ethernet1/2 link-speed auto
set network interface ethernet ethernet1/2 link-duplex auto
set network interface ethernet ethernet1/2 link-state auto

set network interface ethernet ethernet1/3 layer3 ipv4 ndp-proxy disabled
set network interface ethernet ethernet1/3 layer3 ip 10.0.150.13/29
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
set network interface ethernet ethernet1/6 layer3 ip 10.0.250.22/26
set network interface ethernet ethernet1/6 layer3 mtu 1500
set network interface ethernet ethernet1/6 comment "MGT"
set network interface ethernet ethernet1/6 link-speed auto
set network interface ethernet ethernet1/6 link-duplex auto
set network interface ethernet ethernet1/6 link-state auto

# Loopback Interface
set network interface loopback units loopback.1 ip 10.0.250.104/32
set network interface loopback units loopback.1 comment "MGMT_LOOPBACK"

!=====================================================
! Security Zone Configuration
!=====================================================

# Define Security Zones
set zone finance network layer3 ethernet1/1.100
set zone finance network layer3 ethernet1/1.110
set zone transit network layer3 ethernet1/2
set zone transit network layer3 ethernet1/3
set zone management network layer3 ethernet1/6

# Zone Protection Profiles
set zone finance network zone-protection-profile Zone-Protection-Finance
set zone transit network zone-protection-profile Zone-Protection-Transit
set zone management network zone-protection-profile Zone-Protection-Management

!=====================================================
! Virtual Router & Routing Configuration
!=====================================================

# Virtual Router
set network virtual-router default interface ethernet1/1.100
set network virtual-router default interface ethernet1/1.110
set network virtual-router default interface ethernet1/2
set network virtual-router default interface ethernet1/3
set network virtual-router default interface loopback.1

# Static Routes
set network virtual-router default routing-table ip static-route TO_FSP_PROXY interface ethernet1/2 destination 10.0.200.0/26 nexthop ip-address 10.0.150.3
set network virtual-router default routing-table ip static-route TO_SERVICES interface ethernet1/2 destination 10.0.200.0/26 nexthop ip-address 10.0.150.3
set network virtual-router default routing-table ip static-route TO_INTERNET interface ethernet1/2 destination 0.0.0.0/0 nexthop ip-address 10.0.150.3

# OSPF Configuration
set network virtual-router default protocol ospf enable yes
set network virtual-router default protocol ospf router-id 10.0.250.104
set network virtual-router default protocol ospf area 0.0.0.0 interface ethernet1/2 enable yes
set network virtual-router default protocol ospf area 0.0.0.0 interface ethernet1/2 passive no
set network virtual-router default protocol ospf area 0.0.0.0 interface ethernet1/3 enable yes
set network virtual-router default protocol ospf area 0.0.0.0 interface ethernet1/3 passive no
set network virtual-router default protocol ospf area 0.0.0.0 interface loopback.1 enable yes
set network virtual-router default protocol ospf area 0.0.0.0 interface loopback.1 passive yes
set network virtual-router default protocol ospf enable yes
set network virtual-router default protocol ospf reject-default-route yes
set network virtual-router default protocol ospf redistribute connected enable yes

!=====================================================
! Security Policies
!=====================================================

# Allow Finance to FSP
set rulebase security rules FINANCE_TO_FSP from finance
set rulebase security rules FINANCE_TO_FSP to transit
set rulebase security rules FINANCE_TO_FSP source any
set rulebase security rules FINANCE_TO_FSP destination 10.0.200.104
set rulebase security rules FINANCE_TO_FSP application any
set rulebase security rules FINANCE_TO_FSP service any
set rulebase security rules FINANCE_TO_FSP action allow
set rulebase security rules FINANCE_TO_FSP log-start yes
set rulebase security rules FINANCE_TO_FSP log-end yes

# Allow Finance to DNS via FSP
set rulebase security rules FINANCE_TO_DNS from finance
set rulebase security rules FINANCE_TO_DNS to transit
set rulebase security rules FINANCE_TO_DNS source any
set rulebase security rules FINANCE_TO_DNS destination 10.0.200.102
set rulebase security rules FINANCE_TO_DNS application dns
set rulebase security rules FINANCE_TO_DNS service application-default
set rulebase security rules FINANCE_TO_DNS action allow
set rulebase security rules FINANCE_TO_DNS log-start no
set rulebase security rules FINANCE_TO_DNS log-end yes

# Allow Finance to DHCP
set rulebase security rules FINANCE_TO_DHCP from finance
set rulebase security rules FINANCE_TO_DHCP to transit
set rulebase security rules FINANCE_TO_DHCP source any
set rulebase security rules FINANCE_TO_DHCP destination 10.0.200.101
set rulebase security rules FINANCE_TO_DHCP application dhcp
set rulebase security rules FINANCE_TO_DHCP service application-default
set rulebase security rules FINANCE_TO_DHCP action allow
set rulebase security rules FINANCE_TO_DHCP log-start no
set rulebase security rules FINANCE_TO_DHCP log-end yes

# Allow Finance to Voice Gateway for VoIP
set rulebase security rules FINANCE_TO_VOICE from finance
set rulebase security rules FINANCE_TO_VOICE to transit
set rulebase security rules FINANCE_TO_VOICE source any
set rulebase security rules FINANCE_TO_VOICE destination 10.0.200.103
set rulebase security rules FINANCE_TO_VOICE application sip
set rulebase security rules FINANCE_TO_VOICE service application-default
set rulebase security rules FINANCE_TO_VOICE action allow
set rulebase security rules FINANCE_TO_VOICE log-start no
set rulebase security rules FINANCE_TO_VOICE log-end yes

# Block Finance to General Network
set rulebase security rules BLOCK_FINANCE_TO_GENERAL from finance
set rulebase security rules BLOCK_FINANCE_TO_GENERAL to transit
set rulebase security rules BLOCK_FINANCE_TO_GENERAL source any
set rulebase security rules BLOCK_FINANCE_TO_GENERAL destination 10.0.0.0/8
set rulebase security rules BLOCK_FINANCE_TO_GENERAL application any
set rulebase security rules BLOCK_FINANCE_TO_GENERAL service any
set rulebase security rules BLOCK_FINANCE_TO_GENERAL action deny
set rulebase security rules BLOCK_FINANCE_TO_GENERAL log-start yes
set rulebase security rules BLOCK_FINANCE_TO_GENERAL log-end yes

# Block Default Rule
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
set high-availability group 2 mode active-passive
set high-availability group 2 configuration-synchronization enabled yes
set high-availability group 2 peer-ip 10.0.250.21
set high-availability group 2 election-option priority secondary
set high-availability group 2 preemptive yes
set high-availability group 2 state-synchronization enabled yes

# Floating IP Addresses
set high-availability group 2 virtual-address ethernet1/1.100 ip 10.0.100.1/25
set high-availability group 2 virtual-address ethernet1/1.110 ip 10.0.110.1/25
set high-availability group 2 virtual-address ethernet1/2 ip 10.0.150.9/29
set high-availability group 2 virtual-address ethernet1/3 ip 10.0.150.9/29

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
set deviceconfig system snmp-setting system name "SIC-MDF2-FNFW-01"
set deviceconfig system snmp-setting system location "MDF2, SeaIce Creamery HQ"
set deviceconfig system snmp-setting system contact "admin@seaice.local"
set deviceconfig system snmp-setting snmp-manager 10.0.250.72
```