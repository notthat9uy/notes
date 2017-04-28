DD-WRT AP Setup

# Setup
## Basic Setup
### WAN Setup
Set "Wan Connection Type" to Disabled

### Network Setup
Configure IPs for desired network topology

Under Network Address Server Settings (DHCP), set "DHCP Type" to DHCP Forwarder. Change the "DHCP Server" to point to Gateway

## Advanced Routing
### Operating Mode
Change "Operating Mode" to Router

Save changes

# Wireless
## Basic Settings
Set "Wireless Mode" to AP
Set appropriate "Wireless Network Name(SSID)"

## Wireless Security
Set "Security Mode" to WPA2 Personal

Set "WPA Algorithm" to AES

Set appropriate "WPA shared key"

Save changes

# Services
## Services
Disable all services

# Security
## Firewall
### Block WAN Requests
Disable everything besides "Filter Multicast"
Save changes

### Firewall Protection
Disable "SPI Firewall"
Save changes

# Administration
## Management
Enable Info Site Password Protection
Disable Routing

Save changes

Reboot router
