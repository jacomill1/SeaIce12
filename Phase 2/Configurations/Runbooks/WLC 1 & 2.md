## Primary Wireless LAN Controller Configuration (SIC-MDF1-WLC-01)

```
!=====================================================
!
! __        _____ _____   ____             __ _       
! \ \      / /_ _|  ___| / ___|___  _ __  / _(_) __ _ 
!  \ \ /\ / / | || |_   | |   / _ \| '_ \| |_| |/ _` |
!   \ V  V /  | ||  _|  | |__| (_) | | | |  _| | (_| |
!    \_/\_/  |___|_|     \____\___/|_| |_|_| |_|\__, |
!                                               |___/ 
!=====================================================
!
! Cisco 3504 Wireless LAN Controller (Primary)
!
!=====================================================
! System Configuration
!=====================================================
config sysname SIC-MDF1-WLC-01
config network webmode enable
config network mgmt-via-wireless disable
config network ap-discovery nat-ip-only disable

! Management IP Configuration
config interface address management 10.0.250.41 255.255.255.192 10.0.250.1

! Service Port Configuration
config interface address service-port 10.0.250.141 255.255.255.192

! Set timezone
config time timezone PST -8 0
config time ntp server 1 10.0.200.21

! Set admin user
config mgmtuser add admin SeaIce@dmin123! read-write

! Enable SSH
config network ssh enable

!=====================================================
! Interface Configuration
!=====================================================
! Corporate Wireless Interface
config interface create Corporate_WLAN 30
config interface address Corporate_WLAN 10.0.30.31 255.255.252.0 10.0.30.1
config interface vlan Corporate_WLAN 30
config interface dhcp Corporate_WLAN primary 10.0.200.101
config interface port Corporate_WLAN 1

! Guest Wireless Interface
config interface create Guest_WLAN 40
config interface address Guest_WLAN 10.0.40.31 255.255.252.0 10.0.40.1
config interface vlan Guest_WLAN 40
config interface dhcp Guest_WLAN primary 10.0.200.101
config interface port Guest_WLAN 1

!=====================================================
! Link Aggregation Configuration
!=====================================================
config lag enable
config lag port-channel 1 mode on
config lag add port 1
config lag add port 2

!=====================================================
! RF Profile Configuration
!=====================================================
! 5GHz RF Profile
config rf-profile create 5GHz SIC-5GHz
config rf-profile channel width SIC-5GHz 40
config rf-profile channel dca-min-energy-rssi SIC-5GHz -70
config rf-profile rx-sop threshold SIC-5GHz auto
config rf-profile tx-power-control-thresh-v1 SIC-5GHz -70
config rf-profile data-rates 802.11a mandatory SIC-5GHz 12
config rf-profile data-rates 802.11a supported SIC-5GHz 12 18 24 36 48 54
config rf-profile data-rates 802.11a disabled SIC-5GHz 6 9
config rf-profile dca-channel-list 802.11a add SIC-5GHz 36,40,44,48,52,56,60,64,100,104,108,112,116,120,124,128,132,136,140
config rf-profile trap-threshold clients SIC-5GHz enable 30 40

! 2.4GHz RF Profile
config rf-profile create 24GHz SIC-2.4GHz
config rf-profile channel width SIC-2.4GHz 20
config rf-profile channel dca-min-energy-rssi SIC-2.4GHz -65
config rf-profile rx-sop threshold SIC-2.4GHz auto
config rf-profile tx-power-control-thresh-v1 SIC-2.4GHz -65
config rf-profile data-rates 802.11b mandatory SIC-2.4GHz 12
config rf-profile data-rates 802.11b supported SIC-2.4GHz 12 18 24 36 48 54
config rf-profile data-rates 802.11b disabled SIC-2.4GHz 1 2 5.5 6 9 11
config rf-profile dca-channel-list 802.11b add SIC-2.4GHz 1,6,11
config rf-profile trap-threshold clients SIC-2.4GHz enable 30 40

!=====================================================
! AP Group Configuration
!=====================================================
config wlan apgroup add NORTH-WING
config wlan apgroup interface-mapping add NORTH-WING 1 Corporate_WLAN
config wlan apgroup interface-mapping add NORTH-WING 2 Guest_WLAN
config wlan apgroup rf-profile assign NORTH-WING 802.11a SIC-5GHz
config wlan apgroup rf-profile assign NORTH-WING 802.11b SIC-2.4GHz

config wlan apgroup add SOUTH-WING
config wlan apgroup interface-mapping add SOUTH-WING 1 Corporate_WLAN
config wlan apgroup interface-mapping add SOUTH-WING 2 Guest_WLAN
config wlan apgroup rf-profile assign SOUTH-WING 802.11a SIC-5GHz
config wlan apgroup rf-profile assign SOUTH-WING 802.11b SIC-2.4GHz

config wlan apgroup add EAST-WING
config wlan apgroup interface-mapping add EAST-WING 1 Corporate_WLAN
config wlan apgroup interface-mapping add EAST-WING 2 Guest_WLAN
config wlan apgroup rf-profile assign EAST-WING 802.11a SIC-5GHz
config wlan apgroup rf-profile assign EAST-WING 802.11b SIC-2.4GHz

config wlan apgroup add CENTRAL
config wlan apgroup interface-mapping add CENTRAL 1 Corporate_WLAN
config wlan apgroup interface-mapping add CENTRAL 2 Guest_WLAN
config wlan apgroup rf-profile assign CENTRAL 802.11a SIC-5GHz
config wlan apgroup rf-profile assign CENTRAL 802.11b SIC-2.4GHz

!=====================================================
! WLAN Configuration
!=====================================================
! Corporate WLAN
config wlan create 1 SIC-Corp SIC-Corp
config wlan ssid 1 SIC-Corp
config wlan interface 1 Corporate_WLAN
config wlan security wpa enable 1
config wlan security wpa wpa2 enable 1
config wlan security wpa wpa2 ciphers aes enable 1
config wlan security wpa akm 802.1x enable 1
config wlan qos 1 platinum
config wlan broadcast-ssid enable 1
config wlan wmm allow 1
config wlan ccx aironetiesupport enable 1
config wlan enable 1

! Guest WLAN
config wlan create 2 SIC-Guest SIC-Guest
config wlan ssid 2 SIC-Guest
config wlan interface 2 Guest_WLAN
config wlan security wpa enable 2
config wlan security wpa wpa2 enable 2
config wlan security wpa wpa2 ciphers aes enable 2
config wlan security wpa akm psk enable 2
config wlan security wpa akm psk set-key ascii SeaIceGuestAccess! 2
config wlan qos 2 bronze
config wlan broadcast-ssid enable 2
config wlan wmm allow 2
config wlan ccx aironetiesupport enable 2
config wlan enable 2

!=====================================================
! RADIUS Server Configuration for 802.1X
!=====================================================
config radius auth add 1 10.0.200.31 1812 ascii RadiusSecretKey!
config radius auth add 2 10.0.200.32 1812 ascii RadiusSecretKey!
config wlan radius_server auth add 1 1
config wlan radius_server auth add 1 2
config radius fallback-test mode passive
config radius fallback-test username seaice-probe

!=====================================================
! High Availability Configuration
!=====================================================
config redundancy unit primary
config redundancy mode sso
config redundancy peer-ip 10.0.250.42
config redundancy peer-timeout 100
config redundancy interface-check management

!=====================================================
! Advanced Settings
!=====================================================
! Set country code
config country US

! Configure RF channels
config advanced 802.11a channel global auto
config advanced 802.11b channel global auto

! Configure transmit power
config advanced 802.11a txpower global auto
config advanced 802.11b txpower global auto

! Configure RRM
config advanced 802.11a coverage data rssi-threshold -80
config advanced 802.11a coverage voice rssi-threshold -80
config advanced 802.11a coverage level global 2
config advanced 802.11a coverage exception global 25
config advanced 802.11a profile clients global 12
config advanced 802.11a profile noise global -70
config advanced 802.11a profile utilization global 80

config advanced 802.11b coverage data rssi-threshold -80
config advanced 802.11b coverage voice rssi-threshold -80
config advanced 802.11b coverage level global 2
config advanced 802.11b coverage exception global 25
config advanced 802.11b profile clients global 12
config advanced 802.11b profile noise global -70
config advanced 802.11b profile utilization global 80

! Enable both radio bands
config 802.11a enable network
config 802.11b enable network

! Save Configuration
save config
```

## Secondary Wireless LAN Controller Configuration (SIC-MDF2-WLC-01)


```
!=====================================================
!
! __        _____ _____   ____             __ _       
! \ \      / /_ _|  ___| / ___|___  _ __  / _(_) __ _ 
!  \ \ /\ / / | || |_   | |   / _ \| '_ \| |_| |/ _` |
!   \ V  V /  | ||  _|  | |__| (_) | | | |  _| | (_| |
!    \_/\_/  |___|_|     \____\___/|_| |_|_| |_|\__, |
!                                               |___/ 
!=====================================================
!
! Cisco 3504 Wireless LAN Controller (Secondary)
!
!=====================================================
! System Configuration
!=====================================================
config sysname SIC-MDF2-WLC-01
config network webmode enable
config network mgmt-via-wireless disable
config network ap-discovery nat-ip-only disable

! Management IP Configuration
config interface address management 10.0.250.42 255.255.255.192 10.0.250.1

! Service Port Configuration
config interface address service-port 10.0.250.142 255.255.255.192

! Set timezone
config time timezone PST -8 0
config time ntp server 1 10.0.200.22

! Set admin user
config mgmtuser add admin SeaIce@dmin123! read-write

! Enable SSH
config network ssh enable

!=====================================================
! Interface Configuration
!=====================================================
! Corporate Wireless Interface
config interface create Corporate_WLAN 30
config interface address Corporate_WLAN 10.0.30.32 255.255.252.0 10.0.30.1
config interface vlan Corporate_WLAN 30
config interface dhcp Corporate_WLAN primary 10.0.200.101
config interface port Corporate_WLAN 1

! Guest Wireless Interface
config interface create Guest_WLAN 40
config interface address Guest_WLAN 10.0.40.32 255.255.252.0 10.0.40.1
config interface vlan Guest_WLAN 40
config interface dhcp Guest_WLAN primary 10.0.200.101
config interface port Guest_WLAN 1

!=====================================================
! Link Aggregation Configuration
!=====================================================
config lag enable
config lag port-channel 1 mode on
config lag add port 1
config lag add port 2

!=====================================================
! RF Profile Configuration
!=====================================================
! 5GHz RF Profile
config rf-profile create 5GHz SIC-5GHz
config rf-profile channel width SIC-5GHz 40
config rf-profile channel dca-min-energy-rssi SIC-5GHz -70
config rf-profile rx-sop threshold SIC-5GHz auto
config rf-profile tx-power-control-thresh-v1 SIC-5GHz -70
config rf-profile data-rates 802.11a mandatory SIC-5GHz 12
config rf-profile data-rates 802.11a supported SIC-5GHz 12 18 24 36 48 54
config rf-profile data-rates 802.11a disabled SIC-5GHz 6 9
config rf-profile dca-channel-list 802.11a add SIC-5GHz 36,40,44,48,52,56,60,64,100,104,108,112,116,120,124,128,132,136,140
config rf-profile trap-threshold clients SIC-5GHz enable 30 40

! 2.4GHz RF Profile
config rf-profile create 24GHz SIC-2.4GHz
config rf-profile channel width SIC-2.4GHz 20
config rf-profile channel dca-min-energy-rssi SIC-2.4GHz -65
config rf-profile rx-sop threshold SIC-2.4GHz auto
config rf-profile tx-power-control-thresh-v1 SIC-2.4GHz -65
config rf-profile data-rates 802.11b mandatory SIC-2.4GHz 12
config rf-profile data-rates 802.11b supported SIC-2.4GHz 12 18 24 36 48 54
config rf-profile data-rates 802.11b disabled SIC-2.4GHz 1 2 5.5 6 9 11
config rf-profile dca-channel-list 802.11b add SIC-2.4GHz 1,6,11
config rf-profile trap-threshold clients SIC-2.4GHz enable 30 40

!=====================================================
! AP Group Configuration
!=====================================================
config wlan apgroup add NORTH-WING
config wlan apgroup interface-mapping add NORTH-WING 1 Corporate_WLAN
config wlan apgroup interface-mapping add NORTH-WING 2 Guest_WLAN
config wlan apgroup rf-profile assign NORTH-WING 802.11a SIC-5GHz
config wlan apgroup rf-profile assign NORTH-WING 802.11b SIC-2.4GHz

config wlan apgroup add SOUTH-WING
config wlan apgroup interface-mapping add SOUTH-WING 1 Corporate_WLAN
config wlan apgroup interface-mapping add SOUTH-WING 2 Guest_WLAN
config wlan apgroup rf-profile assign SOUTH-WING 802.11a SIC-5GHz
config wlan apgroup rf-profile assign SOUTH-WING 802.11b SIC-2.4GHz

config wlan apgroup add EAST-WING
config wlan apgroup interface-mapping add EAST-WING 1 Corporate_WLAN
config wlan apgroup interface-mapping add EAST-WING 2 Guest_WLAN
config wlan apgroup rf-profile assign EAST-WING 802.11a SIC-5GHz
config wlan apgroup rf-profile assign EAST-WING 802.11b SIC-2.4GHz

config wlan apgroup add WEST-WING
config wlan apgroup interface-mapping add WEST-WING 1 Corporate_WLAN
config wlan apgroup interface-mapping add WEST-WING 2 Guest_WLAN
config wlan apgroup rf-profile assign WEST-WING 802.11a SIC-5GHz
config wlan apgroup rf-profile assign WEST-WING 802.11b SIC-2.4GHz

!=====================================================
! WLAN Configuration
!=====================================================
! Corporate WLAN
config wlan create 1 SIC-Corp SIC-Corp
config wlan ssid 1 SIC-Corp
config wlan interface 1 Corporate_WLAN
config wlan security wpa enable 1
config wlan security wpa wpa2 enable 1
config wlan security wpa wpa2 ciphers aes enable 1
config wlan security wpa akm 802.1x enable 1
config wlan qos 1 platinum
config wlan broadcast-ssid enable 1
config wlan wmm allow 1
config wlan ccx aironetiesupport enable 1
config wlan enable 1

! Guest WLAN
config wlan create 2 SIC-Guest SIC-Guest
config wlan ssid 2 SIC-Guest
config wlan interface 2 Guest_WLAN
config wlan security wpa enable 2
config wlan security wpa wpa2 enable 2
config wlan security wpa wpa2 ciphers aes enable 2
config wlan security wpa akm psk enable 2
config wlan security wpa akm psk set-key ascii SeaIceGuestAccess! 2
config wlan qos 2 bronze
config wlan broadcast-ssid enable 2
config wlan wmm allow 2
config wlan ccx aironetiesupport enable 2
config wlan enable 2

!=====================================================
! RADIUS Server Configuration for 802.1X
!=====================================================
config radius auth add 1 10.0.200.31 1812 ascii RadiusSecretKey!
config radius auth add 2 10.0.200.32 1812 ascii RadiusSecretKey!
config wlan radius_server auth add 1 1
config wlan radius_server auth add 1 2
config radius fallback-test mode passive
config radius fallback-test username seaice-probe

!=====================================================
! High Availability Configuration
!=====================================================
config redundancy unit secondary
config redundancy mode sso
config redundancy peer-ip 10.0.250.41
config redundancy peer-timeout 100
config redundancy interface-check management

!=====================================================
! Advanced Settings
!=====================================================
! Set country code
config country US

! Configure RF channels
config advanced 802.11a channel global auto
config advanced 802.11b channel global auto

! Configure transmit power
config advanced 802.11a txpower global auto
config advanced 802.11b txpower global auto

! Configure RRM
config advanced 802.11a coverage data rssi-threshold -80
config advanced 802.11a coverage voice rssi-threshold -80
config advanced 802.11a coverage level global 2
config advanced 802.11a coverage exception global 25
config advanced 802.11a profile clients global 12
config advanced 802.11a profile noise global -70
config advanced 802.11a profile utilization global 80

config advanced 802.11b coverage data rssi-threshold -80
config advanced 802.11b coverage voice rssi-threshold -80
config advanced 802.11b coverage level global 2
config advanced 802.11b coverage exception global 25
config advanced 802.11b profile clients global 12
config advanced 802.11b profile noise global -70
config advanced 802.11b profile utilization global 80

! Enable both radio bands
config 802.11a enable network
config 802.11b enable network

! Save Configuration
save config
```

