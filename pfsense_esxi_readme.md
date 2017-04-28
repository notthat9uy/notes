Install 6.5 ESXI Hypervisor

Install latest pfSense for ESXI

# Planning your VLANS

Here's a sample VLAN configuration
```
VLAN 10 - LAN network
VLAN 20 - WIFI
VLAN 30 - Guest network
VLAN 99 - WAN
```

The numbers are arbitrary as long as they are between 1 - 4094

# Configure Switch

Each switch will have their own configuration menu. The process should be the same across all of them:

  - Create the VLAN IDs for switch
  - For each VLAN ID, mark the untagged ports and the tagged (Trunk) port on the switch
  - Assign a PVID to each port

The tagged port will be assigned to the port where pfSense is plugged into

Example:
```
-------------------------------------------------
|  1  |  2  |  3  |  4  |  5  |  6  |  7  |  8  |
-------------------------------------------------
  10T   99U   10U   10U   20U   30U
  20T
  30T
  99T

Where 99U is the modem
      10U are LAN wired computers
      20U is a WIFI AP
      30U is a Guest WIFI AP
```

## The switch settings would have

| VLAN ID | Ports |
|:-------:|-------|
| 10      | 1 3 4 |
| 20      | 1 5   |
| 30      | 1 6   |
| 99      | 1 2   |


## VLAN Configuration
```
VLAN ID 10
-------------------------------------------------
|  1  |  2  |  3  |  4  |  5  |  6  |  7  |  8  |
-------------------------------------------------
   T           U     U

VLAN ID 20
-------------------------------------------------
|  1  |  2  |  3  |  4  |  5  |  6  |  7  |  8  |
-------------------------------------------------
   T                       U

VLAND ID 30
-------------------------------------------------
|  1  |  2  |  3  |  4  |  5  |  6  |  7  |  8  |
-------------------------------------------------
   T                             U


VLAN ID 99
-------------------------------------------------
|  1  |  2  |  3  |  4  |  5  |  6  |  7  |  8  |
-------------------------------------------------
   T     U
```

## PVID Settings

|Port|PVID|
|:--:|:--:|
|  1 | 1  |
|  2 | 99 |
|  3 | 10 |
|  4 | 10 |
|  5 | 20 |
|  6 | 30 |
|  7 | 1  |
|  8 | 1  |

NOTE: Ports 7 and 8 are not defined and are left to a DEFAULT VLAN of 1. Since VLAN 1 is not configured to interface with pfSense, ports 7 and 8 are isolated from the rest of the network



# Configure ESXI
Login to your ESXI instance.

Under the Navigator panel, click on Networking. Inside Port groups, click "Add Port Groups", one for each VLAN ID. Following the example, there will be 4 Port groups added and defined

```
Name:           VLAN WAN
VLAN ID:        99
Virtual Switch: vSwitch0 (Change this to match pfSense switch)

Name:           VLAN LAN
VLAN ID:        10
Virtual Switch: vSwitch0 (Change this to match pfSense switch)

Name:           VLAN WIFI
VLAN ID:        20
Virtual Switch: vSwitch0 (Change this to match pfSense switch)

Name:           VLAN GUEST
VLAN ID:        30
Virtual Switch: vSwitch0 (Change this to match pfSense switch)
```

Now under the Navigator panel, click on "Virtual Machines" -> pfSense instance.
Next click on "Edit" near the top and remove all existing network adapters. Add the 4 network adapters by click on "Add network adapter" 4 times. In each newly added Network Adapter, you can use the drop down menu to select all the newly created network devices.

Now start up pfsense. When it finishes booting, it will display all the newly added interfaces, em0 to em3. Use the MAC addresses to distinguish between each VLAN

Next pfSense will ask

```
Do you want to set up VLANs first?

If you are not going to use VLANs, or only for optional interfaces, you should
say no here and use the webConfigurator to configure VLANs later, if required.

Do you want to set up VLANs now [y|n]?
```

Enter n

```text
Enter the WAN interface name or 'a' for auto-detection
```

Enter the interface that has the MAC matching the adapter with VLAN ID 99

```text
Enter the LAN interface name or 'a' for auto-detection
```

Enter the interface that has the MAC matching the adapter with VLAN ID 10

For OPT1 and OPT2, you can assign the adapters with VLAN ID 20 and 30


In the pfSense menu, enter 2) for "Set Interface(s) IP Addresses"

In this menu, you set up each adapter to your desired network topology

Example:
```
WAN  to DHCP
LAN  to static IP
OPT1 to static IP
OPT2 to static IP
```
